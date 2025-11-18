# DAY 6 DEBUGGING CHALLENGES

## Challenge 1: Wrong Page Returned
**Bug:** Method returns wrong page object type
**Solution:** Return correct next page class

## Challenge 2: NullPointerException in BasePage
**Bug:** Forgot to call super(driver) in page constructor
**Solution:** Always call `super(driver)` first

## Challenge 3: Locator Not Found
**Bug:** Locator is private and can't be accessed
**Solution:** That's correct! Locators should be private. Use methods instead.

## Challenge 4: Test Failing After Locator Change
**Bug:** Updated locator in page but test still fails
**Solution:** Verify you updated the right locator in the right page class

## Challenge 5: Circular Dependency
**Bug:** HomePage tries to create LoginPage which creates HomePage
**Solution:** Don't create circular page dependencies, return new instances only when navigating
