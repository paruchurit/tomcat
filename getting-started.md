---

copyright:
  years: 2017
lastupdated: "2017-11-09"

---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}
{:codeblock: .codeblock}
{:pre: .pre}
{:screen: .screen}
{:tip: .tip}
{:download: .download}
{:app_name: data-hd-keyref="app_name"}

# Getting started tutorial
{: #getting_started}

* {: download} Congratulations, you deployed a Hello World sample application on {{site.data.keyword.Bluemix}}!  To get started, follow this step-by-step guide. Or, <a class="xref" href="http://bluemix.net" target="_blank" title="(Download sample code)"><img class="hidden" src="../../images/btn_starter-code.svg" alt="Download application code" />download the sample code</a> and explore on your own.

By following the Tomcat getting started tutorial, you'll set up a development environment, deploy an app locally and on {{site.data.keyword.Bluemix}}, and integrate a database service in your app.

## Before you begin
{: #prereqs}

You'll need the following:
* [{{site.data.keyword.Bluemix_notm}} account](https://console.ng.bluemix.net/registration/)
* [Cloud Foundry CLI ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://github.com/cloudfoundry/cli#downloads){: new_window}
* [Eclipse IDE for Java EE Developers ![External link icon](../../icons/launch-glyph.svg "External link icon")](http://www.eclipse.org/downloads/packages/eclipse-ide-java-ee-developers/neon2){: new_window}
* [Git ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://git-scm.com/downloads){: new_window}
* [Maven ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://maven.apache.org/download.cgi){: new_window}
* [Apache Tomcat version 8.0.41 ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://tomcat.apache.org/download-80.cgi#8.0.41 ){: new_window}


## Step 1: Clone the sample app
{: #clone}

Now you're ready to start working with the sample Tomcat app. Clone the repository and change to the directory to where the sample app is located.
```
git clone https://github.com/IBM-Bluemix/get-started-tomcat
```
{: pre}

```
cd get-started-tomcat
```
{: pre}

Peruse the files in the *get-started-tomcat* directory to familiarize yourself with the contents.

## Step 2: Run the app locally
{: #run_locally}

You must install the dependencies and build a .war file as defined in the pom.xml file to run the app.

Install the dependencies.

```
mvn clean install  
```
{: pre}


Copy GetStartedTomcat.war from the `target` directory into your `tomcat-install-dir` `webapps` directory.

Run the app.  
```
<tomcat-install-dir>/bin/startup.bat|.sh
```
{: pre}

View your app at: http://localhost:8080/GetStartedTomcat/

Use `shutdown.bat|.sh` to stop your app.  Note you may need to give the commands execute permission.
{: tip}

## Step 3: Prepare the app for {{site.data.keyword.Bluemix_notm}} deployment
{: #prepare}

To deploy to {{site.data.keyword.Bluemix_notm}}, it can be helpful to set up a manifest.yml file. The manifest.yml includes basic information about your app, such as the name, how much memory to allocate for each instance and the route. We've provided a sample manifest.yml file in the `get-started-tomcat` directory.

Open the manifest.yml file, and change the `name` from `GetStartedTomcat` to your app name, <var class="keyword varname" data-hd-keyref="app_name">app_name</var>.
{: download}

  ```
  applications:
  - name: GetStartedTomcat
    random-route: true
    memory: 256M
    path: target/GetStartedTomcat.war
    buildpack: java_buildpack
  ```
  {: codeblock}

In this manifest.yml file, **random-route: true** generates a random route for your app to prevent your route from colliding with others.  If you choose to, you can replace **random-route: true** with **host: myChosenHostName**, supplying a host name of your choice. [Learn more...](/docs/manageapps/depapps.html#appmanifest)
{: tip}

## Step 4: Deploy the app
{: #deploy}

You can use the Cloud Foundry CLI to deploy apps.

Choose your API endpoint

```
cf api <API-endpoint>
```
{:pre}

Replace the *API-endpoint* in the command with an API endpoint from the following list.

|Region          |API endpoint                             |
|:---------------|:-------------------------------|
| US South       |https://api.ng.bluemix.net     |
| United Kingdom | https://api.eu-gb.bluemix.net  |
| Sydney         | https://api.au-syd.bluemix.net |
| Frankfurt     | https://api.eu-de.bluemix.net |

Login to your {{site.data.keyword.Bluemix_notm}} account:

```
cf login
```
{: pre}

If you cannot log in using the `cf login` or `bx login` commands because you have a federated user ID, use either the `cf login --sso` or `bx login --sso` commands to log in with your single sign on ID. See [Logging in with a federated ID](https://console.bluemix.net/docs/cli/login_federated_id.html#federated_id) to learn more.

From within the *get-started-tomcat* directory push your app to {{site.data.keyword.Bluemix_notm}}
```
cf push
```
{: pre}

This can take around two minutes. If there is an error in the deployment process you can use the command `cf logs <Your-App-Name> --recent` to troubleshoot.

When deployment completes you should see a message indicating that your app is running.  View your app at the URL listed in the output of the push command.  You can also issue the
  ```
cf apps
  ```
  {: pre}
  command to view your apps status and see the URL.

## Step 5: Add a database
{: #add_database}

Next, we'll add a NoSQL database to this application and set up the application so that it can run locally and on {{site.data.keyword.Bluemix_notm}}.

1. In your browser, log in to {{site.data.keyword.Bluemix_notm}} and go to the Dashboard. Select your application by clicking on its name in the **Name** column.
2. Click on **Connections** and then **Connect new**.
3. In the **Data & Analytics** section, select **Cloudant NoSQL DB** and then create the service.
4. Go to **Apps > Your App > Connections** and then choose **Connect Existing**.
5. Select **Restage** when prompted. {{site.data.keyword.Bluemix_notm}} will restart your application and provide the database credentials to your application using the `VCAP_SERVICES` environment variable. This environment variable is available to the application only when it is running on {{site.data.keyword.Bluemix_notm}}.

Environment variables enable you to separate deployment settings from your source code. For example, instead of hardcoding a database password, you can store this in an environment variable which you reference in your source code. [Learn more...](/docs/manageapps/depapps.html#app_env)
{: tip}

## Step 6: Use the database
{: #use_database}

We're now going to update your local code to point to this database. We'll store the credentials for the services in a properties file. This file will get used ONLY when the application is running locally. When running in {{site.data.keyword.Bluemix_notm}}, the credentials will be read from the `VCAP_SERVICES` environment variable.

1. In your browser, go to {{site.data.keyword.Bluemix_notm}} and select **Apps > _your app_ > Connections > Cloudant > View Credentials**.

2. Copy and paste just the `url` from the credentials to the `url` field of the `cloudant.properties` file, and save the changes.
  ```
  cloudant_url=https://123456789 ... bluemix.cloudant.com
  ```
  {:pre}

3. Restart the server

Refresh your browser view at: http://localhost:8080/GetStartedTomcat/. Any names you enter into the app will now get added to the database.

  Your local app and the {{site.data.keyword.Bluemix_notm}} app are sharing the database. Names you add from either app will appear in both when you refresh the browsers.


Remember, if you don't need your app live on {{site.data.keyword.Bluemix_notm}}, stop the app so you don't incur any unexpected charges.
{: tip}  
