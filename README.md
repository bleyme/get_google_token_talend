# Getting Google access token with Talend
This repository contains the Talend job GetGoogleToken. The goal is to make sure we always have an access token ready to be used to call any Google endpoints in Talend. Can be re used in other jobs.

# Table of contents
  * [Requirements](#requirements)
  * [Features](#features)
  * [Installation](#installation)
     * [Create Google Cloud project](#create-google-cloud-project)
     * [Define GoogleContexts.csv location](#define-googlecontexts.csv-location)
     * [Configure GoogleContexts.csv](#configure-googlecontexts.csv)
  * [Basic Overview](#basic-overview)
  * [Usage](#usage)
     * [Example 1 getting Gmail metadata](#example-1-getting-gmail-metadata)
     * [Example 2 getting Calendar ressources ](#example-2-getting-calendar-resources )
  * [Tips and tricks](#tips-and-tricks)
     * [Updating required scopes](#updating-required-scopes)
     * [Revoking access to a token or application](#revoking-access-to-a-token-or-application)

# Requirements
 - [Talend Open Studio for MDM](https://fr.talend.com/products/mdm/mdm-open-studio/)
 - [Google Cloud Platform](https://console.cloud.google.com/)

# Features
 - Generate URL with scopes required for your project
 - Getting authorization code generated above to request an access token
 - Refreshing access token with a refresh token 
 - Made tokens visible in a file GoogleContexts.csv to lets your other jobs using access tokens easily

# Installation

## Create Google Cloud project
Create a Google Project at this url: https://console.cloud.google.com/
Then go to APIs & Services > Credentials and create an OAuth Client ID of type "Other":
![enter image description here](https://lh3.googleusercontent.com/HWRDRWaRjIx92RWhojm1GYSQAe37j4jAAVg76oBBzSi4egv0YRjpZZsvODva0cA5k4rA9bGfHs3bXlmwII_t71BjgspxMqAb9q0L0qApwkKkVFFaUTpchCjkov6ct2RzOQHj5GUJY-KdpGpQ1WR2W0MOuSKpUsEkaxtWZOOtCky8fkGUS5V9CQLM-NyugHw_POa5Ir_4e6xueJWR9YSDR0DrioOtcyUf8Sj7UC_8pA6saoPfBiI-L4SVwPh_G7VcswXvZSi1rrKQxKtYIUU39QSm10wsN-reeE0gEU4gLMljWUCrGZGWoiOggseO9ICqAkuqT3q4ZAb1rPHjjKuDTKWBXm5Hdhc187ec7Hm3kA3sJHlZMl0sWuam2ktnUlIFXMoRCL8I-PtA2OGIRSsTGQSQXAnb-GAnjFkFu0MCqOhGtW0-n2RSAlZyIJ3IFrWaXXr7rPu6RrOxpT3ODtDGYdkD8xfEvcgDpf3N9uWkq_wZXNcfs2FK089ST9EsbIHh32W-b2XokSuHQzFb-8KK0nosv7mhl-JmG38qF2S8iE4hwhCunLxLz8WTa0qkuOTs5Us8CJFFFb8z3WaFk03qOMNrVs9OpnBaQjV7keOm=w382-h285-no)

![enter image description here](https://lh3.googleusercontent.com/nzI5ZYZeXHN4Ow1w94SS-l3Ue3zywrfXxPBvi0Y70X12wEPHmwTkgxEjXHCB0xXpwpfAmFnY7guVUQsSRgxb3wJjIemVvSsCWqiyBbDalyjjwC34Oy46ubJKbEkzUJId8OjEDmIY_yhItER2Wg8srAcpq6VgiQhJVvlWIJnmQK5DYTQvWzauQr0Zw6rm9Oz-JDTnRLONIYqMkf5IyqXS3b-Ra5IWs1Os-IYZX2rD0VXulQwOYPP6h4IsJsHnVeCT0uuZMG-diAy6iZrLlpplk0USqF6yLdVaeb9ukbDFgw9LcOATOuR_w2yGyHvYi-Ip4cr_FOzR8X-DC0UKxyq3TtegT-9iCSMjRM33vXpF7I13FtQhoFNZ98rnsyISqF4rMFQGot-U2szG7eyHTB0hx8M_OgqwG94IKIhMmVrdJq0K-LvhVVbj8QOo84j18QhLgbqtduthJlH2KlO80HHmdzu5xI51V5nwvC_HiDk9Bs_OgYCEtuLZj4oBDyugLDP87QKqSH0nw36OPaxBn4LulbuXiYg17cgz8K3Mh9zhxfVg7589aQlUmPNfs_6pICGteQmx0eeFO2eBeIUsono6NftQgYwTPBod1788Wi0Z=w470-h233-no)

From here Cloud Console will provide you the following :
 - Client ID
 - Client Secret

We will re use those above in our Talend job to get access token in order to made API restful request.

Let's suppose your goal is to have an overview of your Google Drive usage using REST API. To allow Talend requesting Drive, you need to authorize Cloud Console project:

Go to APIs & Services > Library:
![enter image description here](https://lh3.googleusercontent.com/ku3IQhpwe9KySbsB_CO2a9C96M3TMoSOCn0n5wuhez6QU8Q4L8bLLTkgk3lnAXE1ydAckE6kp-amywr0mV832zIrvsMnsJOdI-L483atXFphXU46PGvfGmZL7yzgNTTHpRgF46oca8YPm42FYsAT1fXDKLczLBGxQiSgax-LJETIwxn0uxBl_lXR6ybcZOZdSvAaC6egz7Q5TWmYP52N4rtW79Y-TYliEekVaCjrWeQfDFw37wWGXMNgOUxD30yDfXr7g6RsgPcD_R-ujnwfXJJrp_7LvYBKZ4NbHCTCPC7b9hwl5eWx8DVqZYs04tUmyOnAxzRYt_MfDGFGkb-MTv5vb05eaC4Q_e2hMQd88lmJ5LJ4YgcYnF_zddcCp2gWd7bopS4diEeYKDiEiLUmZ6zMWOOjWskdhecmj3gIYEljVgC-idBwZWe3G-B44nn1mHLVUUQvM2VON4h4jGbxm6D6-qCA9svICSMIOe1Yw8pW_WyKsN5o0XO_tRCAz_oFzDhf_ZcSiSQtA6-oI4-MzxS78o4vL8lm19hEjSekFFlMm5ETSdn2Yib1d9fYj2XoMQIsVuvaeXDwIKjsCRqsr6KW2ETBkqiNAKyqVcfL=w479-h217-no)
Search for **Drive** and enable.
    
## Define GoogleContexts.csv location
Here we need to specify in Talend job where will be located your data and GoogleContexts.

Edit Talend job and in context tab, specify the folder location of GoogleContexts.csv in path context:
![enter image description here](https://lh3.googleusercontent.com/TAOHv5Aul6rnPk5VjE22-mTO-GqwO_xm5h72ENQgoMPS62tHJctmmMcWXA71TmRCJoAn7vhFs-eXBfTLlh2RqZkWZ5fS33RgPUk3Y92jMvw2YRSjOksicRTsp0gJ6fmSGXJGtBGKQk-puvSyaNeuzEY9cdjHnS22fQfdR4Pxo25ndYTz_J9ojGwC2nvAs1ge2vIgK8OWSAzu8zLgTESlLh2lD5o7i0SG0HxJVCPy8KTFKVXse8JdccSnlhqMMT6Ip0Wor1_tsO3ruUuqj-Q8DSonb5GNWNsom6ZexCDpSyYqIhvIJUTV8Gq6K0yU4uHEXkNBSMO7utzZ5vycTnJ1Ss16VI_8_IodC618VB8BSVspDICV75uyuIpScfUjDzboT9OYak3X7CfidXiBpzTgobUA1dQZcxYcmPFZW2h5W0yiIoorsGuSFWasNsB3-Wmfg6dLxd5PkVz3iu-LBolTCkxH2xx65OFAdKsIjNqlPZCC7QUr8hhL6UVkKO8qKKEjop5oPel66lNDw5xG_-SD1qRmBiNuZ6YctnHHE5Xn6SJSC20YxEzigF3bGkvRXeN8dfdWppRYWX2YufN_ZTl1G50aMuGPHbtd30VXhmjH=w1048-h298-no)

In this example the location of GoogleContexts.csv file is located in the following folder: C:\talend\

## Configure GoogleContexts.csv
Once Google cloud project created and API enabled, create a file GoogleContexts.csv (or download it from this repo) in location specified in [Define GoogleContexts.csv location](#define-googlecontexts.csv-location). 

Fill it with the right credentials:

    client_id;YOUR_CLIENT_ID
    client_secret;YOUR_SECRET_ID
    scope;YOUR_SCOPES

Replace **YOUR_CLIENT_ID** and **YOUR_SECRET_ID** with those you created on [Create Google Cloud project](#create-google-cloud-project) section.

You need to know the scope required for your project. We still suppose your goal is to do some Google Drive manipulations (with edit rights). In this case your scope will probably be:

    https://www.googleapis.com/auth/drive

So here is how it looks like when configured:

    client_id;YOUR_CLIENT_ID
    client_secret;YOUR_SECRET_ID
    scope;https://www.googleapis.com/auth/drive

By default, the scope defined in this repo is the following: 

    https://www.googleapis.com/auth/plus.login

# Basic Overview
![enter image description here](https://lh3.googleusercontent.com/MCsEwoJBN1ehTXTv0lM8PE7E3ttHyVzpEz2nwOKVoLvZFOMFTOQWbQltX2wMmmWSvNWZDqotiy-B579VBciCbXxcM6kgQauaxTqAOgDapPHTtmEPPhMcsBneQIoj-J8cyZnDmnJbAMsPLNVn8wuaSilt9Dlvis9sFXXuQbTpVWrCafZCj1HjtDSHsqKQ2-vV4-5lps-8oviUb5OhNbJnvCE3xhIChhRczG5KhhYNS_Q9Rbmf0VyzpOWNxr0iT1abSdIFJ0fhs2tlSXa2NvIiMsMCK-nUVzIaBPssxJvCQ83Hu_HzjF8X9B6AGmnirhTPBAN2WEWm4G1Nnjtdwpyx42fLWejXLPqXcrmlNzRRf8A2QzvJNcBc4FzSeqsLftY5VZX4g1mmIN4bk1bZv_2qRkfP_G0ygnHAoZkP7tNvh2Qai--uu1Io98PYAbj4mQeTPyD0FgZ9_c0JQGMmbuvLUYCy6YUB1p20cHuEd3cVaBVfxFeimduyv3aDl7vlwubx9udfmQgxMF1ettYdk5PBkSeFpavVg_h2mCUvGwsn8jlvJivIXfhavAm6BCjlftkp8UcKt0iwfh0wJoDZtrzLim_N5SYGUbWfVVQxac7i=w1521-h652-no)

# Usage
If GoogleContexts.csv is well filled with *client_id, client_secret *and* scope*. Then you can execute this job. You will receive for the first time a message saying the following:

    [statistics] connecting to socket on port 3466
    [statistics] connected
    Refresh token and access token not found.
    https://accounts.google.com/o/oauth2/auth?scope=https://www.googleapis.com/auth/plus.login&state=123456789qwertyui&redirect_uri=urn:ietf:wg:oauth:2.0:oob&response_type=code&client_id=441071126182-kg470tgvp2vr14jk259of6ap0lkh728s.apps.googleusercontent.com&approval_prompt=auto&include_granted_scopes=true&access_type=offline

URL has been generated according to your client id and scopes you defined. The process is the following:
 - Copy paste URL on a browser and get authorization 
 - Copy paste authorization code and put in Talend in message box :
![enter image description here](https://lh3.googleusercontent.com/Qk3B8Da3L_zdRDUbH428G60627iY-cFAwp_g5h308aTLeu37K0BTQa5jBjSZIAeQIUXX4byfelEhH6OBREcwgd7xZzmLLCNlCW3esYk_r3TUViuBVpNMVrNSpJfulNDxkI0GGt9JAobZ9xJPz1vQJDtl7S4Fq3Ion6y4vueBZG4-rbhprr85TaLB0c_DRhkSaeSoJTVFkhGb7WxNyj42ZFZROTKQ3q744g0M1Zg51tjOHyIOqkWeKw5ah5X4pmoOWeAMJ5UZEPSX_sNUkTDfHMvltrTza1UDk0K310ZJZHPjjqtG9VYSJ9ke066qvXaeniV-SGTD8VveoNo8FJ3Rr5cYej_HNFwtAbEX_Zg3nptYmeWppdKtWU2DlEILb_CeY2YQKNBNGysSURJLNC2PPHBgsZYvTMFeOSoUv9Dh1Axf2jfYAScEwJqE2svqXisJvcysOePAkjbC_ajflBgmJvTSw--CbYOFPK4hAajlQfG4uvQKVgwlg-IlzsEy3SVpFN5bMcjGUBgZ2YcfPCeQrKJMORLH1c7RKRFFMjxSFZZaQcbqiaVr-7miNIknR2ggoh2E8dtGpZSyz9dSVkRJSTVrbjiU8DPvZzg6obII=w526-h120-no)

Once it's done, it will automatically get an access token and made it available in GoogleContexts.csv. Nothing else. 
You can see the result in GoogleContexts.csv that is looking like that now:

    client_secret;zd0-dKMjrODHFFAs8leXiNQ4
    redirect_uri;urn:ietf:wg:oauth:2.0:oob
    refresh_token;1/ccXLxSshiUWtFPdZV5AZbZG_IuqQzF5q3X7ASAs_QPE
    scope;https://www.googleapis.com/auth/plus.login
    code;
    path;C:/talend/
    client_id;441071126182-kg470tgvp2vr14jk259of6ap0lkh728s.apps.googleusercontent.com
    access_token;ya29.GltkBTQch8ubkWSHzXN911G4XCCZpaX0AsYuHctRCkJmwbizyCwMuKihWRPcOCTxp7tvvVnE2b09lh_8k5CK5B3MOvbVsZZCT_25mElv5aBRKeI0AZXBJ0TYhFXi

There is no manual operations required anymore. Every time you execute the job, it will update GoogleContexts.csv with an updated access_token.

Let's see two examples below to see how to use in other jobs.

## Example 1 getting Gmail metadata
Available in the following repo: [get_gmail_metadata_talend](https://github.com/bleyme/get_gmail_metadata_talend)

## Example 2 getting Calendar resources 
Available in the following repo: [get_calendar_resources_talend](https://github.com/bleyme/get_calendar_resources_talend)

# Tips and tricks

## Updating required scopes 
You can add several scopes separated by a space in GoogleContexts.csv file:

    scope;https://www.googleapis.com/auth/gmail.readonly https://www.googleapis.com/auth/drive.readonly

If you do that, then you also need to remove access_token and refresh_token fields from GoogleContexts.csv. Or redownload GoogleContexts.csv from scratch and fill client_id, client_secret and scope as defined in [Configure GoogleContexts.csv](#configure-googlecontexts.csv) section.

Then the job will ask you to re authorize your app to access data stored in your Google account. Your access token will have authorization to request on Google APIs according to the scopes you defined and re authorized. Otherwise, request will be forbidden.

## Revoking access to a token or application
At any time, you can revoke access of GetGoogleToken at this url: https://accounts.google.com/issuedauthsubtokens

