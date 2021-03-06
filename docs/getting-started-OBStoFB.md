# Getting Started with AWS Elemental MediaLive - OBS Studio to Facebook Live<a name="getting-started"></a>

This tutorial describes how to ingest a video source from an RTMP push source and generate one RTMP output video and one audio \. MediaLive will send the output to Facebook Live. The output will consist of the following: 

This tutorial uses the default values for most configuration fields in the channel\.

**Note**  
All the text marked as an example in this tutorial is just that—a sample that shows what a piece of information typically looks like\. You must replace each example with the information that is valid for your situation\.

## Prerequisites<a name="getting-started-prerequisites"></a>

Before you can use MediaLive, you need an AWS account and the appropriate permissions to access, create, and view MediaLive components\.

## Architecture
![OBS Studio to Facebook Live](./images/MediaLive_OBSStudiotoFacebookLive.png)

## Step 1: Set Up the Upstream System (OBS Studio)<a name="getting-started-step1"></a>

The upstream system is the local OBS Studio that streams the video to MediaLive\. The upstream system can be anything from an on\-premises appliance that is serving as a "contribution encoder" to an application running on a smart phone\. You must perform some setup of your upstream system before you start working with MediaLive\. 

For the purposes of this tutorial, the upstream system must be capable of sending a video stream via RTMP push\. 

**To set up the upstream system**

1. Luanch your OBS Studio.

1. Add Browser in sources, choose **Create new**\ enter **Browser** click **OK**\. 

1. Enter URL **http://www.clocktab.com**

## Step 2: Set Up the Downstream System (Facebook Live)<a name="getting-started-step2"></a>

In this tutorial, the downstream system (the destination for the output from MediaLive) is Facebook Live. 

You must set up a channel in AWS Elemental MediaPackage, and you must set it up now because you need the two input URLs that AWS Elemental MediaPackage generates\. You enter these input URLs into MediaLive\.

**To set up the downstream system**

1. Sign in to the Facebook at [https://www.facebook.com].

1. Switch to your own fan page.

1. Click **Publishing Tools**, Click **Video Library** in left panel

1. Click **+ Live**

1. Keep the web browser open; don't close it yet\.

   Your Live Stream to the Live API information is show in web page with **Server URL** and **Stream Key**

## Step 3: Create an Input (Elemental MediaLive)<a name="getting-started-step3"></a>

You must create an input\. The input defines how the upstream system provides the source video stream to MediaLive\. In this tutorial, you create an RTMP push input\. 

You must also create an input security group for the input\. This input security group applies the rule "only this specific IP address \(an IP address that you own\) can push to this input on MediaLive\." Without the protection of this rule, any third party could push content to an MediaLive input if they know the IP address and port of the input\. 

**To create an input and input security group**

1. Sign in to the AWS Management Console and open the MediaLive console at [https://console\.aws\.amazon\.com/medialive/](https://console.aws.amazon.com/medialive/)\.

1. In the navigation pane, choose **Inputs**\.

1. On the **Inputs** page, choose **Create input**\.

1. In the **Input details** section, for **Input name**, enter **My OBS RTMP push**\.

1. For **Input type**, choose **RTMP (push)**\. 

1. In the **Input security group** section, choose **Create**\. 

1. In the text box, enter the IP address that you noted in [Step 1: Set Up the Upstream System](#getting-started-step1) of this tutorial\. Enter the address as a CIDR block\. For example, **203\.0\.113\.111/32** or **0.0.0.0/0**\.

1. Choose **Create input security group**\.

1. Choose **Create** to create the input\.

   MediaLive adds the input to the list of inputs and automatically creates two destinations \(one primary and one redundant\)\. These destinations include the URL and port\. For example, **rtmp://18.176.19.105:1935/live/main** and **rtmp://18.178.120.169:1935/live/backup**\. These are the two locations where the upstream system must push the source\. 

1. Make a note of these two addresses because you will need them in [Step 10: Start the Upstream System and the Channel](#getting-started-step8)\.

## Step 4: Set up Key Information<a name="getting-started-step4"></a>

The first step to creating a channel from scratch is to choose the IAM role that MediaLive will use to access the channel when the channel is running \(started\) and specify key characteristics of the input\. Now you are ready to start creating a channel\. The first step is to identify the input\. The channel contains the details that instruct MediaLive how to transcode \(decode and encode\) and package that input into specific outputs\.

The first step to creating a channel from scratch is to choose the IAM role that MediaLive will use to access the channel when the channel is running \(started\) and specify key characteristics of the input\.

**To specify key information for the channel**

1. On the MediaLive console, in the navigation pane, choose **Channels**\.

1. In the **Channels** section, choose **Create channel**\. 

1. In the **Channel and input details** pane, in **General info**, for **Channel name**, enter **FB Live 0131**\.

1. For **IAM role**, choose **Create role from template** and choose **Create IAM role**\. The **Use existing role** list now shows the role **MediaLiveAccessRole**\.

1. Choose **Remember role**\.

1. For **Channel class**, choose **SINGLE_PIPELINE**.


## Step 5: Attach the Input<a name="getting-started-step4b"></a>

Now you are ready to identify the input that the channel will ingest\.

**To attach the input to the channel**

1. On the **Create channel** page, in the navigation pane, for **Input attachments**, choose **Add**\.

1. In **Attach input** , for **Input**, **My OBS RTMP push** \(the input that you created\.\) 

   The **Attachment name** field is automatically populated with the name of the input itself\. You can leave this name as is\. 

1. Choose **Confirm**\. The **Input attachment** section closes, and the **General input settings** section appears\.

## Step 6: Create an RTMP Output Group<a name="getting-started-step5"></a>

Once you have set up the input, you continue with the channel creation by creating an output group\. In this tutorial, you set up an RTMP output group\.

**To create an output group**

1. On the **Create channel** page, in the **Output groups** section, choose **Add**\. 

1. In the **Add output group** section, choose **RTMP**, and then choose **Confirm**\. 

Create an Input (Elemental MediaLive)<a name="getting-started-step3"></a>

1. In the **RTMP group destination A** section  
   + For **URL**, enter the URL that generated for you in [Step 2: Set Up the Downstream System](#getting-started-step2)\. For example, **rtmps://live-api-s.facebook.com:443/rtmp/**\. 
   + For **Stream Name**, enter the Stream Key that generated for you in [Step 2: Set Up the Downstream System](#getting-started-step2)\. For example, **167575214684872?s_bl=1&s_sw=0&s_vt=api-s&a=AbyOQtGU3sXanjCV**\.

## Step 7: Create Your Channel<a name="getting-started-step7"></a>

You have entered the minimum required information, so you are ready to create the channel\.

**To create the channel**
+ On the **Create channel** page, under the **Channel** section, choose **Create channel**\. 

  The **Channel** section reappears and shows the newly created channel, named **MyHLS**\. The state changes to **Creating**, then **Ready**\.

## Step 8: Start the Upstream System and the Channel (OBS Studio)<a name="getting-started-step8"></a>

You can now start the upstream system in order to push the streaming content to MediaLive.

**To start the upstream system**

1. Launch application **OBS Studio**. 

1. On the **Channels** list, choose the channel\.

1. Choose **Settings**\, Choose **Stream**.
   
   + For **Service**, choose **Custom**
   + For **Server**, enter the URL for you in [Step 3: Create an Input (Elemental MediaLive)](#getting-started-step3)\. For example, **rtmp://18.176.19.105:1935/live**\. 
   + For **Stream Key**, enter the content you input for Application name and instance in [Step 3: Create an Input (Elemental MediaLive)](#getting-started-step3)\. For example, **main**\.

1. Click **Strat Streaming**. The output from AWS Elemental MediaLive starts playing\.

## Step - Clean Up<a name="getting-started-step9"></a>

To avoid extraneous charges, delete this channel and input when you have finished working with it\.

**To delete the channel**

1. On the **Channels** page, choose the channel\.

1. If needed, choose **Stop**\.

1. Choose **Delete**\.

1. On the **Inputs** page, choose the input\.

1. Choose **Delete**\.
