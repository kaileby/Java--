import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.chrome.ChromeDriver;

import java.util.List;

public class test {
    public static void main(String[] args) {
        System.setProperty("webdriver.chrome.driver",test.class.getClassLoader().getResource("chromedriver.exe").getPath());

        WebDriver wd=new ChromeDriver();
        wd.get("https://www.lagou.com/zhaopin/Java/?labelWords=label");
        //选中 工作经验为应届毕业生的页面
        String title1_1= "工作经验";
        String title1_2 = "应届毕业生";

        String title2_1 = "学历要求";
        String title2_2 = "本科";

        String title3_1 = "融资阶段";
        String title3_2 = "不限";

        String title4_1 = "公司规模";
        String title4_2 = "不限";

        String title5_1 = "行业领域";
        String title5_2 = "移动互联网";

       clickOptions(wd, title1_1, title1_2);
       clickOptions(wd, title2_1, title2_2);
       clickOptions(wd, title3_1, title3_2);
       clickOptions(wd, title4_1, title4_2);
       clickOptions(wd, title5_1, title5_2);

       //解析页面元素
        extractJobsByPagination(wd);
    }

    private static void extractJobsByPagination(WebDriver wd) {
        List<WebElement> jobEles=wd.findElements(By.className("con_list_item"));//得到指定class属性的li标签
        for (WebElement jobEle:jobEles){
            WebElement moneyEle=jobEle.findElement(By.className("position")).findElement(By.className("money"));
            //System.out.println();
            WebElement companyEle=jobEle.findElement(By.className("company_name"));
            //System.out.println(companyEle.getText());
            System.out.println("公司："+companyEle.getText()+" 薪资："+moneyEle.getText());
        }
        //如果页面可以点击，点击，继续爬取
       WebElement nextpageBtn=wd.findElement(By.className("pager_next"));
        if(!nextpageBtn.getAttribute("class").contains("pager_next_disabled")){
            System.out.println("解析下一页.....");
            nextpageBtn.click();
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            extractJobsByPagination(wd);
        }
    }


    private static void clickOptions(WebDriver wd, String title1, String title2) {
        WebElement choseEle= wd.findElement(By.xpath("//li[@class='multi-chosen']//span[contains(text(),'" + title1 + "')]"));
        //xpath语法： //li:选中所有li标签，【@class='multi-chosen'】 ：筛选条件为class='multi-chose'
        // //span:选中 li标签中内容为工作经验的span标签
        WebElement optionEle=choseEle.findElement(By.xpath("../a[contains(text(),'" + title2 + "')]"));
        optionEle.click();
    }
}
