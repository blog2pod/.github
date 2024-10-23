# blog2pod

## What it does
blog2pod is a self-hosted application that converts web articles and blogs to audio files using AI-based Text-to-Speech (TTS) technology, and then publishes the resulting MP3 files as a podcast feed for you to consume in your podcast player of your choice!

The main reason I created b2p is to give myself a hands free option for listening to articles in the car. My favorite "read it later" app is [Reeder](https://reederapp.com), which doesn't offer an option to listen to the articles in your feed, and the apps that do have that capability, don't integrate with CarPlay or Android Auto for easy selection and playback

blog2pod brings the articles you want to listen to right to your preferred podcast player, which have been designed specifically for listening to spoken-word audio, so they have all the controls you might want. Voice boost, silence skipping, playback speed adjustments, etc..

## How it works
The "frontend" of blog2pod is a Discord bot. To send an article to b2p for processing, you just need to send the url to a channel where the bot is active, prepended with !blog2pod. For example:

<img src="https://i.imgur.com/JxiS0mD.png" alt="Image" width="500"/>


The simplest way to do this consistently is to set up a shortcut on iOS (or the Android equivalent) to share the URL through a Discord Webhook. Here is my [shortcut template](https://www.icloud.com/shortcuts/b68364e6e3874254b10f231a3b68a17e) for iOS. This gives you the ability to send articles to b2p right from your device's built in share sheet

<img src="https://i.imgur.com/Q9wsq5C.png" alt="Image" width="300"/>

Once the url is shared, b2p will generate the audio using the AI platform you configured during setup (Today, b2p supports OpenAI, [ElevenLabs](https://elevenlabs.io/?from=tylerplesetz8843), and Azure OpenAI) and publish it to your podcast feed

<img src="https://i.imgur.com/gZ4m6GE.png" alt="Overcast on iOS" width="300"/>

## Installation

### Discord Bot Setup
1. 

### Docker Compose

[Docker Hub Link](https://hub.docker.com/r/tylerplesetz/blog2pod)

#### Image Tags
| Docker Image Tag                    | AI Platform     |  Source Code    |
|-------------------------------------|-----------------|-----------------|
| `tylerplesetz/blog2pod:latest`      | **Azure OpenAI** | [Source](https://github.com/blog2pod/blog2pod) |
| `tylerplesetz/blog2pod:oai`         | **OpenAI**       | [Source](https://github.com/blog2pod/blog2pod_oai) |
| `tylerplesetz/blog2pod:el`          | **ElevenLabs**   | [Source](https://github.com/blog2pod/blog2pod_el) |


```yaml
version: "3.3"
services:
  blog2pod:
    container_name: blog2pod
    environment:
      - TTS_VOICE=shimmer
      - DISCORD_BOT_TOKEN=<TOKEN>
      - API_KEY=<APIKEY>
      - TTS_DEPLOYMENT=<azure model deployment name> #Optional - Use for Azure OpenAI (:latest)
      - AZURE_ENDPOINT=<azure endpoint url> #Optional - Use for Azure OpenAI (:latest)
    restart: unless-stopped
    volumes:
      - /path/to/podcasts/directory:/blog2pod/completed
    image: tylerplesetz/blog2pod:oai

  b2pserve:
    container_name: b2pserve
    environment:
      - BASEURL=<url>
      - POD_TITLE=blog2pod
      - POD_DESCRIPTION="AI generated audio for articles that I don't have time to read ..."
      - POD_IMAGE=https://i.imgur.com/y2WVeWh.png
      - POD_AUTHOR=codefruit
    restart: unless-stopped
    volumes:
      - /path/to/podcasts/directory:/b2pserve/completed
    ports:
      - "8000:8000"
    image: tylerplesetz/b2pserve:latest
networks: {}
```

#### Environment variables

| Name                | Container | Required?                        | Notes                                                                                                                                           |
| ------------------- | --------- | -------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------- |
| `TTS_VOICE`         | blog2pod  | Yes                              | Used to determine voice used by selected AI platform<br>- OpenAI uses names like "shimmer"<br>- ElevenLabs uses IDs like "XrExE9yKIg1WjnnlVkGX" |
| `API_KEY`           | blog2pod  | Yes                              | API key for your selected AI platform                                                                                                           |
| `DISCORD_BOT_TOKEN` | blog2pod  | Yes                              | Token for your Discord Bot                                                                                                                      |
| `TTS_DEPLOYMENT`    | blog2pod  | latest: yes<br>oai: no<br>el: no | If you're using Azure OpenAI, you'll know what this is                                                                                          |
| `AZURE_ENDPOINT`    | blog2pod  | latest: yes<br>oai: no<br>el: no | If you're using Azure OpenAI, you'll know what this is                                                                                          |
| `BASEURL`           | b2pserve  | Yes                              | Public URL for accessing your podcast feed                                                                                                      |
| `POD_TITLE`         | b2pserve  | Yes                              | Title of your published podcast                                                                                                                 |
| `POD_DESCRIPTION`   | b2pserve  | Yes                              | Description of your published podcast                                                                                                           |
| `POD_IMAGE`         | b2pserve  | Yes                              | Artwork for your published podcast                                                                                                              |
| `POD_AUTHOR`        | b2pserve  | Yes                              | Author of your published podcast                                                                                                                |

### iOS Shortcut Setup (Optional, but recommended)
1. Install the Shortcut using [this link](https://www.icloud.com/shortcuts/b68364e6e3874254b10f231a3b68a17e)
2. Configure your Discord Webhook URL
<img src="https://i.imgur.com/ZxWa5jP.png" alt="Image" width="300"/>

## Pre-release disclaimer

This is pre-release software and anything can break at any time. I make not guarantees about the stability of this software, forward-compatibility of updates, or integrity (both related to and independent of blog2pod). Essentially, use at your own risk and expect there will be rough edges for now.
