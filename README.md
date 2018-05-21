# CICD


![image](http://www.ruanyifeng.com/blogimg/asset/2017/bg2017121901.png)

---

## 1. What is Travis CI?
> Travis CI is a hosted, distributed continuous integration service used to build and test software projects hosted at GitHub.    
>
> Open source projects may be tested at no charge via travis-ci.org.   
>#### Advantage :
>Every time the code changes small, you can see the run results, thus accumulating small changes, instead of merging a large piece of code at the end of the development cycle.

## 2. How to use it?
### 2.1 Basic Step

2.1.1. Using your GitHub account, sign in to GitHub and add the Travis CI app to the repository you want to activate.

2.1.2. Once you’re signed in to Travis CI, and we’ve synchronized your GitHub repositories, go to your profile page and enable the repository you want to build:![image](https://docs.travis-ci.com/images/enable.png)

2.1.3. Add a ==.travis.yml== file to your repository to tell Travis CI what to do. For example: this is a Ruby project that should be built with Ruby 2.2, and the latest versions of JRuby and Rubinius 2.X.
```
language: ruby
rvm:
 - 2.2
 - jruby
 - rbx-3
```
2.1.4. Add the ==.travis.yml== file to git, commit and push, to trigger a Travis CI build, and Check the build status page to see if your build passes or fails.




### 2.2 .travis.yml settings
1. select a programming language
```
language: JAVA

```
Or pick one from the [full list](https://docs.travis-ci.com/user/languages/)
> The best way to determine what infrastructure your build runs on is to set the language.
If you do this your build runs on the default infrastructure (with a few exceptions), which is Container Based Ubuntu 14.04. 

You can explicitly select the default infrastructure by adding 
```
sudo: false
```
 to your ==.travis.yml.==


Here is the simplest.Travis.yml file for Python projects.

```
language: python
script: true
```
The script field specifies the script to run. ``script: true`` means that no script is executed, and the state is directly set to succeed.

The following is a slightly more complex ==.travis.yml.==

```
language: python
sudo: required
before_install: sudo pip install foo
script: py.test
```

### 2.3 Running process
The running process of Travis is very simple. Any project will go through two stages:

    - install: install any dependencies required
    - script: run the build script
   

![image](https://github.com/lin233/others/blob/master/CI-7.png?raw=true)

#### install
If there are multiple scripts, it can be written in the following form.

```
install:
  - bundle install --path vendor/bundle
  - npm install
```
if command1 fails, the whole building will stop and go down.

If you do not need to install, skip the installation stage and set it directly to true.

```
install: true
```

#### script
If there are multiple scripts, it can be written in the following form.

```
script:
- bundle exec rake build
- bundle exec rake builddoc
```

==script== is different from ==install==. If `command1` fails, `command2` will continue to execute. However, the state of the entire construction phase is a failure.



If `command2` can only be executed after `command1` is successful, it should be written as follows.
```
script: bundle exec rake build && bundle exec rake builddoc
```


After the script is completed, the notification and the deployment can also be set up. They are 

#### deployment
The deployed scripts can be executed in the script phase, or Travis can provide quick deployment functions for dozens of common services. For example, if you want to deploy to Github Pages, you can write it down like this.


```
deploy:
  provider: pages
  skip_cleanup: true
  github_token: $GITHUB_TOKEN # Set in travis-ci.org dashboard
  on:
    branch: master
```
When deploying files to a provider, prevent Travis CI from resetting your working directory and deleting all changes made during the build ( `git stash --all`) by adding `skip_cleanup` to your `.travis.yml`:

```
deploy:
  skip_cleanup: true
```

## 3. Practice
We use former homework 3 to test CI environment: [Ref repo](https://github.com/myuaggie/wordladder_restful_security)

in ``.travis.yml`` 

```
language: JAVA

```

We use Maven to build the project

```
mvn install -DskipTests=true -Dmaven.javadoc.skip=true -B -V
mvn test -B

```
result:

![image](https://github.com/lin233/others/blob/master/CI-8.png?raw=true)

Build History:
![image](https://github.com/lin233/others/blob/master/CI-9.png?raw=true)

---

![image](https://ss0.bdstatic.com/70cFuHSh_Q1YnxGkpoWK1HF6hhy/it/u=2168665466,3877876536&fm=27&gp=0.jpg)

## What is Heroku?
> Heroku is a cloud platform as a service (PaaS) supporting several programming languages that is used as a web application deployment model. This tutorial will have you deploying a Java app in minutes. 


## How to use Heroku?
#### The tutorial that you need:
- a free [Heroku account](https://signup.heroku.com/dc).
- [Java 8 ](http://www.oracle.com/technetwork/java/javase/downloads/index.html)installed locally.
- [Maven 3](http://maven.apache.org/download.cgi) installed locally.
#### Set up
Once installed, you can use the `heroku` command from your command shell.


Log in using the email address and password you used when creating your Heroku account:
```
$ heroku login
Enter your Heroku credentials.
Email: java@example.com
Password:
```
#### Prepare the app
To clone the sample application so that you have a local version of the code that you can then deploy to Heroku, execute the following commands in your local command shell or terminal:



```
$ git clone https://github.com/heroku/java-getting-started.git
$ cd java-getting-started
```
You now have a functioning git repository that contains a simple application as well as a `pom.xml` file.


#### Deploy the app
Create an app on Heroku
```
$ heroku create
```
a git remote (called `heroku`) is also created and associated with your local git repository. 

deploy your code:
```
$ git push heroku master

```

 Ensure that at least one instance of the app is running:
```
$ heroku ps:scale web=1
```
you can open the website as follows:
```
$ heroku open
```

#### View logs
View information about your running app using one of the logging commands, `heroku logs --tail`:

Press `Ctrl+C` to stop streaming the logs.

#### Define a Procfile

The `Procfile` in the example app you deployed looks like this:

```
web: java -jar target/java-getting-started-1.0.jar
```

Procfiles can contain additional process types. 

#### Scale the app
You can check how many dynos are running using the `ps` command:

```
$ heroku ps
```
Scaling an application on Heroku is equivalent to changing the number of dynos that are running. Scale the number of web dynos to zero:


```
$ heroku ps:scale web=0
```
Access the app again by hitting refresh on the web tab, or `heroku open` to open it in a web tab. You will get an error message because you no longer have any web dynos available to serve requests.

Scale it up again:
```
$ heroku ps:scale web=1
```

#### Declare app dependencies
The demo app you deployed already has a pom.xml (see it here). Here’s an excerpt:
```
 <dependencies>
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-actuator</artifactId>
    </dependency>
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    ...
  </dependencies>
```

The `pom.xml` file specifies dependencies that should be installed with your application. When an app is deployed, Heroku reads this file and installs the dependencies using the `mvn clean install` command.


#### Run the app locally
make sure you’ve already run `mvn clean install` too:

```
$ heroku local web
```
`heroku local` examines the `Procfile` to determine what command to run.  
Open http://localhost:5000 with your web browser. You should see your app running locally.


#### Push local changes
Modify `pom.xml` to include a dependency for `jscience`. The `<dependencies>` section should look something like this:

```
<dependencies>
    ...
    <dependency>
      <groupId>org.postgresql</groupId>
      <artifactId>postgresql</artifactId>
      <version>9.4.1212</version>
    </dependency>
    <dependency>
      <groupId>com.zaxxer</groupId>
      <artifactId>HikariCP</artifactId>
      <version>2.6.0</version>
    </dependency>
    <dependency>
      <groupId>org.jscience</groupId>
      <artifactId>jscience</artifactId>
      <version>4.3.1</version>
    </dependency>
  </dependencies>
```
Modify `src/main/java/com/example/Main.java` so that it imports this library at the start, by including the following imports:

```
import static javax.measure.unit.SI.KILOGRAM;
import javax.measure.quantity.Mass;
import org.jscience.physics.model.RelativisticModel;
import org.jscience.physics.amount.Amount;
```
And add a `hello` method so that it reads like this:

```
@RequestMapping("/hello")
String hello(Map<String, Object> model) {
    RelativisticModel.select();
    Amount<Mass> m = Amount.valueOf("12 GeV").to(KILOGRAM);
    model.put("science", "E=mc^2: 12 GeV = " + m.toString());
    return "hello";
}
```

Then create a `src/main/resources/templates/hello.html` file with these contents:

```
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org" th:replace="~{fragments/layout :: layout (~{::body},'hello')}">
<body>
  <div class="container">
    <p th:text="${science}"/>
  </div>
</body>
</html>
```

 test locally:
```
$ mvn clean install
$ heroku local web
```
Now deploy. Almost every deploy to Heroku follows this same pattern. First, add the modified files to the local git repository:

```
$ git add .
$ git commit -m "Demo"
$ git push heroku master
$ heroku open hello
```

#### Provision add-ons

##### ClearDB MySQL

>The ClearDB add-on can be installed and up and running in your app in no time! Run the following command to add ClearDB to your application:


```
$ heroku addons:create cleardb:ignite
-----> Adding cleardb to sharp-mountain-4005... done, v18 (free)
```
Retrieve your database URL by issuing the following command:
```
$ heroku config | grep CLEARDB_DATABASE_URL
CLEARDB_DATABASE_URL => mysql://adffdadf2341:adf4234@us-cdbr-east.cleardb.com/heroku_db?reconnect=true
```

Copy the value of the `CLEARDB_DATABASE_URL` config variable.

```
heroku config:set DATABASE_URL='mysql://adffdadf2341:adf4234@us-cdbr-east.cleardb.com/heroku_db?reconnect=true'
Adding config vars:
DATABASE_URL => mysql2://adffd...b?reconnect=true
Restarting app... done, v61.
```




## Practice
- Also use former homework 3 to test CD environment
```
git clone https://github.com/myuaggie/wordladder_restful_security

$ heroku login         (use email and password to login)
$ cd wordladder_restful_security   
$ heroku create wordladder-security      
$ git push heroku master
$ heroku ps:scale web=1 
$ heroku open 

```
- Now we can access the basic web pages that are not related to database.
But our user info used for authorization is stored in mysql database.


- So we need to move forward to set our database:

```
$ heroku addons:create papertrail 
$ heroku addons:create cleardb:ignite   
$ heroku addons

```
![image](https://github.com/lin233/others/blob/master/CD-1.png?raw=true)
```
$ heroku config | grep CLEARDB_DATABASE_URL    (retrieve your new ClearDB database URL)

```
![image](https://github.com/lin233/others/blob/master/CD-2.png?raw=true)

`CLEARDB_DATABASE_URL: mysql://user:password@host.post/databasename`

- Connect to the cleardb according to the given CLEARDB_DATABASE_URL with MySQL Workbench  
![image](https://github.com/lin233/others/blob/master/CD-3.png?raw=true)

- Create table users and insert user info

![image](https://github.com/lin233/others/blob/master/CD-4.png?raw=true)

- Modify the DBConfig class in code

![image](https://github.com/lin233/others/blob/master/CD-5.png?raw=true)

- Up to now, we can access our database successfully 
![image](https://github.com/lin233/others/blob/master/CD-6.png?raw=true)

- But there are still some bugs to be handled

> We use file resources in our app.
But when we deployed it on Heroku, it didn’t work

- The reason is that : 
> When we run our project locally, file resources are picked from an exploded JAR file, which is a regular file.
But on Heroku, it is in the JAR file, so the file we are trying to pick  is not a regular file
- The solution is that : 
>Open and read file resources as an input stream

![image](https://github.com/lin233/others/blob/master/CD-7.png?raw=true)


```
$ git push heroku master
$ heroku open 

```
**- Now we can deploy our project successfully**
![image](https://github.com/lin233/others/blob/master/CD-8.png?raw=true)
