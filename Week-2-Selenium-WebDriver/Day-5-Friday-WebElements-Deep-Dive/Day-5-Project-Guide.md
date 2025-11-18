# DAY 5 PROJECT: DYNAMIC CONTENT HANDLER

## Project: Scrape and Validate Dynamic Web Table

**Task:** Extract all data from dynamic table, handle stale elements, use JavaScript for hidden elements

**Complete Solution:**
```java
public class DynamicContentProject {
    public static void main(String[] args) {
        WebDriver driver = new ChromeDriver();
        JavascriptExecutor js = (JavascriptExecutor) driver;
        
        try {
            driver.get("https://the-internet.herokuapp.com/tables");
            
            // Find all rows
            List<WebElement> rows = driver.findElements(By.xpath("//table[@id='table1']//tr"));
            System.out.println("Total rows: " + (rows.size() - 1)); // Exclude header
            
            // Extract data from each row
            for (int i = 2; i <= rows.size(); i++) {
                WebElement row = driver.findElement(By.xpath("//table[@id='table1']//tr[" + i + "]"));
                
                String lastName = row.findElement(By.xpath(".//td[1]")).getText();
                String firstName = row.findElement(By.xpath(".//td[2]")).getText();
                String email = row.findElement(By.xpath(".//td[3]")).getText();
                
                System.out.println(firstName + " " + lastName + " - " + email);
            }
            
            // Handle dynamic loading
            driver.get("https://the-internet.herokuapp.com/dynamic_loading/2");
            driver.findElement(By.cssSelector("#start button")).click();
            
            WebDriverWait wait = new WebDriverWait(driver, Duration.ofSeconds(10));
            WebElement result = wait.until(ExpectedConditions.visibilityOfElementLocated(By.id("finish")));
            
            System.out.println("\nDynamic content: " + result.getText());
            
        } finally {
            driver.quit();
        }
    }
}
```

**Learning Outcomes:**
- findElements() for collections
- Handling dynamic content
- XPath for table navigation
- Explicit waits for dynamic elements
