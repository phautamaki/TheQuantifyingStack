# Oura to Apple Health Sync
The following [iOS shortcuts](#ios-shortcuts) add the functionality for syncing [Oura Ring](https://ouraring.com/) heart rate variablility ([HRV](https://en.wikipedia.org/wiki/Heart_rate_variability)) and resting heart rate (RHR) data from the [Oura API](https://cloud.ouraring.com/docs/) to [Apple Health](https://www.apple.com/ios/health/). 

## Important notice
Oura Ring provides HRV values calculated as "root Mean Square of Successive Differences" (rMSSD) whereas Apple Health assumes the saved HRV values represent "Standard Deviation of NN intervals" (SDNN) values as that is what the Apple Watch is using. **By utilising the shortcuts below, you acknowledge they are saving rMSSD values to Health instead of the expected SDNN values, which may cause weird interpretations in some apps utilising HRV data from Health.** Furthermore, in case you also have an Apple Watch in addition to Oura Ring, the different types of HRV values will get mixed in Health (although they are still separable by data source if needed).

There is a detailed blog post comparing the [differences and similarities between rMSSD and SDNN values](https://www.hrv4training.com/blog/heart-rate-variability-hrv-features-can-we-use-sdnn-instead-of-rMSSD-a-data-driven-perspective-on-short-term-variability-analysis) regarding stress and recovery estimation and the TL;DR version could be the following: "Both values can be used to track physiological stress over time as they produce similar trends in relation to their baselines. rMSSD is slightly more accurate in sensing acute stressors, which makes it the preferred option." Additionally, a blog post from Oura describes [why Oura uses the entire night of HRV data](https://ouraring.com/blog/hrv-data/), which is summarised in the last sentence: "...to provide you with the most reliable physiological stress assessment based on HRV".

Because the main usage of the nightly HRV (and HR) values is to estimate physiological stress and recovery, it is highly likely that adding the Oura HRV values to Health will increase the accuracy of recovery estimation because 
1. rMSSD is the more accurate estimator of acute stressors and 
2. the frequency of measurements in Oura is much higher (every 5 min) than with Apple Watch (every 2 hours at best, if not forced with Mindfulness session), which diminishes the variation in measurement conditions (circadian rhythm, sleep stages, see the above blog post from Oura for more details) between each night.

**Therefore, calculating the average of all available nightly HRV values regardless of the type (rMSSD/SDNN) will likely yield a very accurate estimation of recovery when compared to baseline of similar combination of data.**

Note that because of the difference in measurement frequencies, the volume of rMSSD values is going to be much higher each night, making it the major contributing factor also regarding averages. This should be beneficial because it is the preferred and more accurate value of the two.

## Prerequisites
* Minimum: iPhone, Oura Ring
* Optional: A specialised app for inspecting and analysing HRV/RHR/recovery data is highly recommended, although the data and trends are visible also in Health. See also [Example setup](#example-setup).

## iOS Shortcuts
The functionality consists of 3 separate shortcuts that you should install based on your needs:

1. [Sync Oura RHR & HRV](https://www.icloud.com/shortcuts/cd4567465a154b75a23f0bc0de9ce9e4)
    * Does the heavy lifting (syncing). Defaults to today (=previous night) but can also take a custom date as a parameter (utilised in history sync).
    * Can be called manually or via automation multiple times a day; in case the data has already been synced, the shortcut silently exits.
        * Protip: Hook this shortcut to the event of closing the Oura app by creating a personal automation from the Shortcuts app in order to make the data import as seamless as possible. Additionally, toggle the "Notify When Run" option off from the automation's settings to limit the unnecessary notifications to minimum (toggle available since iOS 15.4).
    * Note: You should have your [Oura Personal Access Token](https://cloud.ouraring.com/personal-access-tokens) at hand (or at clipboard) when importing this: it asks for the token during setup (although you can always update it later inside the shortcut).
2. [Debug Sync Oura RHR & HRV](https://www.icloud.com/shortcuts/d94f3b90c0cf4b0e91973548705acf1e)
    * The **Sync Oura RHR & HRV** only alerts the user in case the data is not yet available in the Oura API so that the user knows to try again later. Otherwise it does its thing in the background. However, if the sync doesn't seem to work, **Debug Sync Oura RHR & HRV** can be used to make some sense of the return values gotten from the **Sync Oura RHR & HRV** shortcut. It's not much but it's honest work.
    * Asks to select your copy of the **Sync Oura RHR & HRV** shortcut during setup.
    * Note: This debug script will actually run the **Sync Oura RHR & HRV** and "translate" the return value to a more informative alert.
3. [Sync Oura RHR & HRV History](https://www.icloud.com/shortcuts/b1962f63b0f74cce98bdc45bbf50854e)
    * In case you want to sync also the historical Oura data to Health (e.g. to immediately have a comparable baseline of the last 60 days or so), **Sync Oura RHR & HRV History** shortcut can help with that. When run, it asks for the inclusive start and end dates, loops through each day in between and uses the **Sync Oura RHR & HRV** shortcut to sync data for each day by passing the date as a parameter.
    * Asks to select your copy of the **Sync Oura RHR & HRV** shortcut during setup.
    * Note: This shortcut hasn't been tested with intervals longer than a few days. It may be best to start with an interval of max 2 weeks and go up from there if it works without problems.

## Example setup
For me, it's not convenient to actively take morning HRV measurements because of hectic mornings (and because it's one more task to do each day). Nightly HRV values are easy, passive alternative to this and with the added data from Oura also quite accurate. Therefore, I'm currently relying on the combination of

1. Oura Ring, main gadget for tracking sleep and nightly HR/HRV (recovery)
2. Apple Watch, main gadget for tracking daily activity and workouts, as well as daily HR/HRV (exertion)
3. [Athlytic](https://athlytic.github.io/athlyticapp/), an app that tracks recovery and exertion based on HR and HRV data available in Health.

## Wait a minute, doesn't Oura app already export data to Health natively?
Yes it does! However, it writes the heart rate values to Health's Heart Rate category instead of Resting Heart Rate category as these shortcuts are doing. Furthermore, the Oura HRV values are not exported to Health at all, probably because of the abovementioned difference in value types (rMSSD vs. SDNN). It may be the situation changes some point in the future making these shorcuts obsolete, but in the meantime, these will do the job just fine.