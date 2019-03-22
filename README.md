# JIRATestNGListener for TestNG

JIRATestNGListener for TestNG is a Java library for creating proper artifact for Jenkins plugin.
Required JAVA 8 or higher.

## Installation
Add rows below in pom.xml file
```xml
<repositories>
    <repository>
        <id>adapter-java-v2-testng-mvn-repo</id>
        <url>https://raw.github.com/at-lab-development/adapter-java-v2-testng/mvn-repo</url>
        <snapshots>
            <enabled>true</enabled>
            <updatePolicy>always</updatePolicy>
        </snapshots>
    </repository>
....
</repositories>
```
```xml
<dependencies>
    <dependency>
        <groupId>com.epam.jira</groupId>
        <artifactId>adapter-java-testng</artifactId>
        <version>2.0-SNAPSHOT</version>
     </dependency>
....
</dependencies>
```
## Usage
There is a few different ways to use a library:
1) Using @Listener on test class
```java
@Listeners(JIRATestNGListener.class)
public class YourTestClass {
....
}
```
2) Add proper listener in testng.xml
```xml
<suite name="your_suite_name">
    <listeners>
        <listener class-name="com.epam.testng.JIRATestNGListener"/>
    </listeners>
....
</suite>
```

3) If you are going to add current listener thrue the TestNG runner configuration
```java
public class Runner {
    public static void main(String[] args) {
        TestNG testNG = new TestNG();
        testNG.addListener((ITestNGListener) new JIRATestNGListener());
....
        testNG.run();
    }
}
```

## Test results mapping
Test result statuses in TestNG is not equal for the statuses in QASpace. 
Plugin provides next way of mapping:

| TestNG status | QASpace status |
| ------------- | ------------- |
| Excluded  | Out of Scope  |
| Scipped  | Untested  |
| Failed  | Failed |
| Passed  | Passed  |


## Features
+ JIRATestKey annotaion
    Necessarily attribute of annotation is a key, whick must be taken from JIRA ticket.
```java
@Test
@JIRATestKey(key = "JIRA ticket")
 public void test1() {
....
}
```
   Also, there is a flag accordinaly to which value execution and preparation of test results could be skipped.

```java
@Test
@JIRATestKey(key = "JIRA ticket", disabled = true)
 public void test1() {
....
}
```
+ Test execution proccess

   If test data is delivered using parameters or data provider, for that test is going to be formed one final result, e.g. if one collection of test data is failed, then all test is going to be marked is failed. 
+ Attachment

   To add attachment use static method addAttachment() in JIRAAttachment class, which takes two parametrs - the first one is ```File``` object and the second is a ```String``` which should contain a JIRA ticket. After the testing procces all attachments is going to be mapped to the proper JIRA tickets and added to testresult.xml file.
    Too add attachment to the JIRA ticket add row bellow:
```java
....
JIRAAttachment.addAttachment(((TakesScreenshot)driver).getScreenshotAs(OutputType.FILE), "test1");
....
```
## Implemented functionality
- [x] Attachments
- [x] Annotation approach
- [x] Multithreading
## Deploying
For continuous developing and deploying create in ```.m2```  folder in your local machine ``` setting.xml```  with following credentials:
```xml
<settings>
  <servers>
    <server>
      <id>github</id>
      <username>Your github login</username>
      <password>Your github password</password>
    </server>
  </servers>
</settings>
```
Also, do not forget to correct the following strings into ```pox.xml``` file if you are going to deploy library into another github repository.
```xml
<repositoryName>adapter-java-v2-testng-demo</repositoryName>
<repositoryOwner>at-lab-development</repositoryOwner>
```
**This library include in ```pox.xml``` file TestNG dependency with scope marked as provided. That could cause some vulnerabilities**
