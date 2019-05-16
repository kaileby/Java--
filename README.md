# Java-
使用 Selenium 模拟人浏览浏览器的行为，调用 ChromeDrive 打开浏览器，爬取信息
## 用 JAVA 编写万能爬虫



## 场景

通过 selenium 实现模拟人为操作，并根据工作经验、学历要求、公司规模、行业领域抓取 xxx 网站的薪资范围；



## 环境准备

* `JDK`
* `IDEA`
* `Chrome Driver`
* `Selenium`

```xml
<dependency>
	<groupId>org.seleniumhq.selenium</groupId>
    <artifactId>selenium-server</artifactId>
    <version>3.141.59</version>
</dependency>
```

## 流程

* step 1

  配置 pom.xml,使用 Maven 自动下载浏览器→使用webDriver唤起浏览器,打开网站

* Step 2：分析页面内容所在的位置

  F12：分析目标网站的爬取的内容所在的标签；

* Step 3: 解析页面

  使用 `Xpath` 获取指定内容所在的标签元素

  实现自动翻页



## 思路

使用 selenium 模仿人浏览浏览器的行为来抓取网页

* 打开 拉钩网：

  ```Java
  	WebDriver wd=new ChromeDriver();
      wd.get("https://www.lagou.com/zhaopin/Java/?labelWords=label");
  ```

* 根据自定义要求得到指定页面

  ```Java
  private static void clickOptions(WebDriver wd, String title1, String title2) {
          WebElement choseEle= wd.findElement(By.xpath("//li[@class='multi-chosen']//span[contains(text(),'" + title1 + "')]"));
          //xpath语法： //li:选中所有li标签，【@class='multi-chosen'】 ：筛选条件为class='multi-chose'
          // //span:选中 li标签中内容为工作经验的span标签
          WebElement optionEle=choseEle.findElement(By.xpath("../a[contains(text(),'" + title2 + "')]"));
          optionEle.click(); //模拟用户点击
      }
  ```

  PS: 注意在调用  Xpath 的 contains()函数时 的 title 的转换

* 解析网页，爬取想要的内容

  ```Java
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
              extractJobsByPagination(wd);//同质页面，递归爬取
          }
      }
  ```

  预期得到招聘公司的薪资、公司名称等信息；

  * 在浏览器后台发现，这些信息都在 `con_list_item`的 `li` 标签中，薪资在一个 `class` 为 money 的 span 子标签中，公司名称在一个 `calss` 为 `company_name`的标签中；通过 `gettext` 就能获得标签的文本内容；
  * 若爬取的信息需要点击 `下一页`才能得到，则 使用`nextpageBtn.click()`模拟 单击下一步.

  

## 遇到的错误

* `chromeDriver 与 浏览器版本不符；`

  `Starting ChromeDriver 2.46.628402 (536cd7adbad73a3783fdc2cab92ab2ba7ec361e1) on port 30930，Only local connections are allowed.`

  解决方法参见：<https://www.jianshu.com/p/40027de48c5b>

  chromev72-74 版本使用 v2.64版本的ChromeDriver
