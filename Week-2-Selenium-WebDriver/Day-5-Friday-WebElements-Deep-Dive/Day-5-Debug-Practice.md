# DAY 5 DEBUGGING CHALLENGES

## Challenge 1: Stale Element After Refresh
**Bug:** Element becomes stale after page refresh
**Solution:** Re-find element after any page change

## Challenge 2: Empty List Ignored
**Bug:** Trying to access index 0 of empty list
**Solution:** Check `!list.isEmpty()` before accessing

## Challenge 3: JavaScript Not Executing
**Bug:** Forgot to cast driver to JavascriptExecutor
**Solution:** `JavascriptExecutor js = (JavascriptExecutor) driver;`

## Challenge 4: Disabled Element Won't Click
**Bug:** Element is disabled
**Solution:** Use JavaScript: `js.executeScript("arguments[0].click();", element);`

## Challenge 5: Wrong Attribute Name
**Bug:** Getting null because attribute doesn't exist
**Solution:** Verify attribute name in DevTools first
