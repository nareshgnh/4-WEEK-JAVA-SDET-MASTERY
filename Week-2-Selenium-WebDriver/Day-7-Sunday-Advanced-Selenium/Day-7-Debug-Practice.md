# DAY 7 DEBUGGING CHALLENGES

## Challenge 1: Headless Mode Failing
**Bug:** Test passes with UI but fails in headless
**Solution:** Set proper window size: `--window-size=1920,1080`

## Challenge 2: Screenshot Not Saving
**Bug:** Directory doesn't exist
**Solution:** Create screenshots directory first: `Files.createDirectories(Paths.get("screenshots"))`

## Challenge 3: RemoteWebDriver Connection Failed
**Bug:** Grid hub not running
**Solution:** Start hub first: `java -jar selenium-server.jar hub`

## Challenge 4: Different Browser Behavior
**Bug:** Test works in Chrome but not Firefox
**Solution:** Browser-specific waits/locators may be needed

## Challenge 5: Performance Still Slow
**Bug:** Using Thread.sleep() everywhere
**Solution:** Replace with WebDriverWait and ExpectedConditions
