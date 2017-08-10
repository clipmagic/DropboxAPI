# ProcessDropboxAPI
## System requirements

* PHP 5.6.4 or greater
* Composer
* The PHP mbstring extension

## General information
This module enables you to access a single Dropbox (www.dropbox.com) account to upload and/or download files. What you can do is determined by the App preferences. 

All kudos to (https://github.com/kunalvarma05/dropbox-php-sdk) for the PHP API.

## First steps
* Visit Refer (https://www.dropbox.com/developers) and read the documentation. 
* Log into Dropbox and create a new application. It's recommended to only allow the App access to a specified folder
* Make a note of the App key, the App secret and the name of the Dropbox folder

## Installation
* Download the zip file into your site/modules folder then expand the zip file. 
* Next, login to ProcessWire > go to Modules > click "Refresh". You should see a note that a new module was found. Install the ProcessDropboxAPI module. 
* Configure the module with you App key, App secret and your Call Back URL

You need to generate Dropbox access token to enable your site to communicate with the nominated Dropbox account. Dropbox will generate a token for you or you can create a page for the front end of your ProcessWire site with a template to submit the token request to Dropbox, eg:

```
    <?php namespace ProcessWire;
    
    $drop = $modules->get('ProcessDropboxAPI');
    
    if ($input->get->code && $input->get->state) {
        $code = $sanitizer->pageName($input->get->code);
        $state = $sanitizer->pageName($input->get->state);
    
        //Fetch the AccessToken
        $accessToken = $drop->getAccessToken($input->get->code, $input->get->state);
    
        $page->body = "Copy/paste this code into the module configuration: " .  $accessToken;
        $page->button ="";
    
    } else {
        $page->button =  "<p><a href='" . $drop->getAuthURL() . "'>Log in with Dropbox</a></p>";
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
    $drop = $modules->get('ProcessDropboxAPI');
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


