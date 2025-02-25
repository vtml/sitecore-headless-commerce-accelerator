# Wooli solution

## Wooli commerce accelerator:
* Enables a JSS-based eCommerce site that is headless, meaning it is decoupled from Sitecore servers
* Empowers productivity on day one through source code that saves teams weeks of development time
* Allows front-end JavaScript developers to quickly develop and adjust eCommerce website features independently from Sitecore back-end developers
* Uses REACT to accelerate the development process with code re-use
* Provides a professionally–branded, custom storefront UI
* Offers a proven alternative to the Sitecore Experience Accelerator (SXA)-based storefront

----
## Installation Guide

### Prerequisites
* Make sure these packages are installed on your PC:
* `Solr 7.2.1` (installing the service with NSSM is recommended)
* `Node 10.15.3 x64`
* `Java jre-8u191 x64`
* `Postman 6.6.1`
* Machine should have at least 8, and 12-16 RAM for comfort work.

### Packages
Make sure you use these packages during installation:

* Sitecore Experience Platform 9.1 Update1 ([download](https://dev.sitecore.net/Downloads/Sitecore_Experience_Platform/91/Sitecore_Experience_Platform_91_Update1.aspx))
* Install Sitecore Commerce Update 1 ([download](https://dev.sitecore.net/Downloads/Sitecore_Commerce/91/Sitecore_Experience_Commerce_91_Initial_Release.aspx))
* Sitecore JavaScript Services 11.1.0 (Sitecore JavaScript Services Server for Sitecore 9.0) ([download](https://dev.sitecore.net/Downloads/Sitecore_JavaScript_Services/110/Sitecore_JavaScript_Services_1110.aspx))
* Sitecore PowerShell Extensions-4.7.2 for Sitecore 8

----
## Steps

### License
Acquire a Sitecore license that authorizes the use of JSS (open it in Notepad and check for "Sitecore.JSS")

### Install Prerequisites
* Install the software specified in the prerequisites

### Install Sitecore Experience Platform 9.1 Update 1
* Download & unpack the Packages for On Premises
* Follow the Installation Guide for XP Single (XP0).
* It’s recommended to set a standard password for the admin sitecore user (password: b).
* Following a successful installation the following sites should be created in IIS:
{sitename}_XConnect
{sitename}
* It is strongly suggested that at this point a backup copy of the Sitecore installation be created (using SIM).

### Install Sitecore Commerce Update 1 or higher
* Make sure you have backed up the sitecore installation prior to installing it, if the script fails you might need to roll everything back.
* It’s important to follow the installation guide to the letter, and pay close attention to the prerequisites, making sure everything specified is installed. Otherwise the script might fail later on and without an informative error message.
* Prior to running the script it needs to be tweaked:

----
### Configuration of the installation process.
* Make sure UserAccount's username/password meets the local username/password requirements, because it will be created if it doesn't exist yet.
* Make sure names and directories for all the required sites in the commerce deployment script correspond the ones that have been installed with sitecore installation.
* Add the CommerceServicesPostfix parameter to postfix these commerce sites in IIS:
* PowerShellExtensionsModuleFullPath & MergeToolFullPath will possibly need to be modified according to their location
* CommerceConnectModuleFullPath
* Replace Sitecore.BizFX.* with the actual folder name (like Sitecore.BizFX.2.0.3) to avoid conflicts with the SDK zip of the same name (the SitecoreBizFxServicesContentPath variable).
* Replace Sitecore.Commerce.Engine.* with the actual folder name (like Sitecore.Commerce.Engine.3.0.163.zip) to avoid conflicts with the SDK zip of the same name (the SitecoreCommerceEnginePath variable).
* Make sure the following ports are available (change if needed):

> CommerceOpsServicesPort   
> CommerceShopsServicesPort     
> CommerceAuthoringServicesPort     
> CommerceMinionsServicesPort

### Advanced configuration of the installation process:
* The **Sitecore Experience Accelerator (SXA)** was not working with Wooli at the time of writing, so it’s recommended that it be removed from installation. To do it, edit the following file: **.\ Configuration\Commerce\ Master_SingleServer.json** by removing these sections:
* Parameters:   

> SXAModuleFullPath     
> SXACommerceModuleFullPath                             
> SXAStorefrontModuleFullPath       
> SXAStorefrontThemeModuleFullPath
> SXAStorefrontCatalogModuleFullPath  

* Variables:    

> SXAMergeInputFile 

* Tasks:    

> InstallSXAFrameworkModule     
> InstallSXAStorefrontModule    

** Also remove these parameters from the script:    
> SXAModuleFullPath     
> SXACommerceModuleFullPath                             
> SXAStorefrontModuleFullPath       
> SXAStorefrontThemeModuleFullPath                
> SXAStorefrontCatalogModuleFullPath

** Important to say the **Sitecore Experience Accelerator** module is not needed so it should not be downloaded either.

* SitecoreBizFx website & app pool's names are hardcoded. To change them, edit the following configs in SIF: **.\Configuration\Commerce\SitecoreBizFx\SitecoreBizFx.json**
* The next to last step RebuildIndexes might fail due to timeout, it’s suggested increasing the timeout by editing the following file: **.\ Modules\SitecoreUtilityTasks\SitecoreUtilityTasks.psm1** and setting **–TimeoutSec** to something like 100000, escecially on a slow env.
* Consecutive messages like "Unable to start the site ..." are indicative of the script waiting for the site to come back from reboot, and are OK if the script recovers from them.
* Pro tip: should one of the steps fail, you don't have to start from scratch, just edit the appropriate json file(s) and remove from it (them) the previous, successfully completed steps and run the script again after fixing the problem that prevented it running the first time.

----

### Known issues of the sitecre commerce server installation

* `The service cannot accept control messages at this time. (Exception from HRESULT: 0x80070425)`
To resolve the issue follow the link to [StackOverflow](https://sitecore.stackexchange.com/questions/12870/the-service-cannot-accept-control-messages-at-this-time-while-installing-sitec). Before you start the installation proccess from the begining you have to delete the folders created by the failed commerce server installation procces (i.e. sitecoreCatalogItemsScope, sitecoreCustomersScope, sitecoreOrdersScope from solr_root_folder/server/solr).

----

### Bootstrapping the sitecore commerce server

Right now Wooli’s catalog and shop names and currency are hardcoded as follows:

| CatalogName | Habitat_Master |
| ------ | ------ |
| ShopName | Wooli |
| SelectedCurrency | USD |

To bootstrap the Commerce Server follow these instructions:

* For Wooli use the authoring server instance of the commerce server (default directory for the instances C:\inetpub\wwwroot).
* In the instance modify the wwwroot\data\Environments\PlugIn.Payments.Braintree.PolicySet-1.0.0.json: set the MerchantId, PublicKey and PrivateKey corresponding to the account you have set up with BrainTree.
* Disable SSL verification from the File > Settings > SSL certification verification in Postman. This settings needs to be turned off.
* Navigate to \CommerceAuthoring_Sc9\wwwroot\config.json and set the value for AntiForgeryEnabled to false.
* Bootstrap the commerce server, using Postman and executing Sitecore Commerce SDK’s commands. Open Postman, import the required sript collections and than execute the required requests. First the GetToken request (it will set the token variable in Postman, the appropriate postman script collection is located in Sitecore.Commerce.Engine.SDK_folder\postman\Shops\Authentication.postman_collection.json), than the Bootstrap Sitecore Commerce request (the appropriate postman script collection is located in Sitecore.Commerce.Engine.SDK_folder\postman\DevOps\SitecoreCommerce_DevOps.postman_collection.json). These steps are mirrored in the installation guide.

----
### Install the Sitecore JavaScript Services 11.1.0

* Download & install the package
* Make sure the following exists in your web.config and if not then add it (system.webServer/handlers section):
`<add verb="*" path="sitecorejss_media.ashx" type="Sitecore.JavaScriptServices.Media.MediaRequestHandler, Sitecore.JavaScriptServices.Media" name="Sitecore.JavaScriptServices.Media.MediaRequestHandler" />`

### Building & deploying Wooli      
* Fetch wooli code base
* To enable debug mode (in case installation goes wrong):

> In src/build.cake, set BuildConfiguration = "Debug";  
> In src/build.cake, set var publishingTargetDir = artifactsBuildDir    
> In src/scripts/webpack/environments/production.js make sure mode=”development” and minimize: false    

* Check src/build.cake if `Sitecore/Parameters.InitParams` are correct for your installation.
* If you use Visual Studion with a version prior to 17 leave msBuildToolVersion parameter as it is, but if you use Visual Studio 19 you have to change the parameter to msBuildToolVersion: MSBuildToolVersion.VS2019.
* Execute `npm istall` inside the .\src folder.
* Add the following NuGet package sources:
    1. https://sitecore.myget.org/F/sc-packages/api/v3/index.json
    2. https://sitecore.myget.org/F/sc-commerce-packages/api/v3/index.json
* Copy sitecore license to ./src folder.
* Restore NuGet packages.
* Execute src/build.ps1.
* It’s advisable to make a backup of the website prior to the next step (with SIM)
* Create a symbol link with `unicorn-wooli` name inside the Root_Sitecore_Folder\App_Data folder to the .\src folder (Root_Sitecore_Folded is the folder where Sitecore is installed, for ex. c:\inetpub\wwwroot\xp0.sc)
* In IIS bind wooli.local to the site, add wooli.local entry for localhost to the hosts list (C:\Windows\System32\drivers\etc\hosts)
* Log in to the sitecore then make a GET request on http://{website}/unicorn.aspx?verb=Sync&log=null&skipTransparentConfigs=false, make sure no errors are displayed (repeat if needed)
* In sitecore content editor modify sitecore/Commerce/Catalog Management/Catalogs item. Select Habitat_Master in the Selected Catalogs field.
* Rebuild all the indexes in sitecore indexing manager.