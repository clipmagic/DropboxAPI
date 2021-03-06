# DropboxAPI
## System requirements

* PHP 5.6.4 or greater
* Composer
* The PHP mbstring extension

## General information
This module enables you to access a single Dropbox (www.dropbox.com) account to upload files from a ProcessWire website.

All kudos to (https://github.com/kunalvarma05/dropbox-php-sdk) for the PHP API.

## First steps
* Visit (https://www.dropbox.com/developers) and read the documentation. 
* Log into Dropbox and create a new application. It's recommended to limit ProcessWire App access to a specified folder
* Make a note of the App key, the App secret and the name of the Dropbox folder

## Installation
* Download the zip file into your site/modules folder then expand the zip file. 
* Next, login to ProcessWire > go to Modules > click "Refresh". You should see a note that a new module was found. Install the DropboxAPI module. 
* Configure the module with you App key, App secret and your Call Back URL

You need to generate Dropbox access token to enable your site to communicate with the nominated Dropbox account. Dropbox will generate a token for you or you can create a page for the front end of your ProcessWire site with a template to submit the token request to Dropbox, eg:

```
    <?php namespace ProcessWire;
    
    $drop = $modules->get('DropboxAPI');
    
    if ($input->get->code && $input->get->state) {
        $code = $sanitizer->text($input->get->code);
        $state = $sanitizer->text($input->get->state);
    
        //Fetch the AccessToken
        $accessToken = $drop->getAccessToken($code, $state);
    
        echo "Copy/paste this code into the module configuration: " .  $accessToken;
    
    } else {
        echo  "<p><a href='" . $drop->getAuthURL() . "'>Log in with Dropbox</a></p>";
    }
    ?>
```

Once you have entered the token in the module configuration, you can unpublish this page.

## Usage

Read the dropbox-php-sdk documentation!

An example template for sending a file to a Dropbox App folder from ProcessWire:
```
    <?php namespace ProcessWire;
    use Kunnu\Dropbox\Dropbox;
    use Kunnu\Dropbox\DropboxApp;
    use Kunnu\Dropbox\DropboxFile;
    
    // send pdf to Dropbox
    $drop = $modules->get('DropboxAPI');
    $app = new DropboxApp($drop->app_key, $drop->app_secret, $drop->authorization_code);
    if ($app) {
        //Configure Dropbox service
        $dropbox = new Dropbox($app);
        $dropboxFile = new DropboxFile('/path/to/myfilename.pdf');
        $file = $dropbox->upload($dropboxFile, "/myfilename.pdf", ['autorename' => true]);
        //Uploaded File meta data
        if ($file) {
            $success = $file->getName() . " uploaded to Dropbox";
            $drop->log($success);
        }
    }

```


