## Description:
TL;DR: Lets the bot answer you with a picture!  

Stable Diffusion API pictures for TextGen with Tag Injection, v.1.0.0  
Based on [Brawlence's extension](https://github.com/Brawlence/SD_api_pics) to [oobabooga's textgen-webui](https://github.com/oobabooga/text-generation-webui) allowing you to receive pics generated by [Automatic1111's SD-WebUI API](https://github.com/AUTOMATIC1111/stable-diffusion-webui). Including improvements from ClayShoaf.


Load it in the `--chat` mode with `--extension sd_api_pictures_tag_injection` alongside `send_pictures` (it's not really required, but completes the picture, *pun intended*).  

The image generation is triggered either:  
- manually through the 'Force the picture response' button while in `Manual` or `Immersive/Interactive` modes OR  
- automatically in `Immersive/Interactive` mode if the words `'send|main|message|me'` are followed by `'image|pic|picture|photo|snap|snapshot|selfie|meme'` in the user's prompt  
- always on in Picturebook/Adventure mode (if not currently suppressed by 'Suppress the picture response')  

## Prerequisites

One needs an available instance of Automatic1111's webui running with an `--api` flag. Ain't tested with a notebook / cloud hosted one but should be possible.   
To run it locally in parallel on the same machine, specify custom `--listen-port` for either Auto1111's or ooba's webUIs.  

## Features:
- Dynamic injection of content into SD prompt upon detection of a preset "translation" string  
- Dynamic injection of content into SD prompt upon detection of a request for character selfie  
- Secondary tags injection, useful to toggle between two styles of images without having to manually type in tags  
- API detection (press enter in the API box)  
- VRAM management (model shuffling)  
- Three different operation modes (manual, interactive, always-on)  
- persistent settings via settings.json

The model input is modified only in the interactive mode; other two are unaffected. The output pic description is presented differently for Picture-book / Adventure mode.  

### Character sheet Stable Diffusion tags

In immersive mode, to help your character maintain a better fixed image, add positive_sd and negative_sd to your character's json file to have Stable Diffusion tags that define their appearance automatically added to Stable Diffusion prompts whenever the extension detects the character was asked to send a picture of itself, ex:

```json
{
	"sd_tags_positive": "24 year old, asian, long blond hair, ((twintail)), blue eyes, soft skin, height 5'8, woman, <lora:shojovibe_v11:0.1>",
	"sd_tags_negative": "old, elderly, child, deformed, cross-eyed"
}
```
If clothing and accessories are permanently affixed and will never be changed in any picture you will request of that character, feel free to add them to this tag too. The extension prompts the character to describe what it is wearing whenever a picture of itself is requested as to keep that aspect dynamic, so adding it in the character json makes it more static.

A good sample prompt to trigger this is "Send me a picture of you", followed or not by more details about requested action and context.

### Description to Stable Diffusion translation

Whenever the Activate SD translations box is checked, the extension will load the translations.json file when a picture is requested, and will check in both the request to the language model, as well as the response of the language model, for specific words listed in the translations.json file and will add words or tags to the Stable Diffusion prompt accordingly, ex:

```json
{
	"pairs": [{
		"descriptive_word": ["tennis"],
		"SD_positive_translation": "tennis ball, rackets, (net), <lora:povTennisPlaying_lora:0.5>",
		"SD_negative_translation": ""
	        },
		{"descriptive_word": ["soccer","football"],
		"SD_positive_translation": "((soccer)), nets",
		"SD_negative_translation": ""
	    }]
}
```

The tags can also include Stable Diffusion LORAs if you have any that are relevant.

### Persistents settings

Create or modify the `settings.json` in the `text-generation-webui` root directory to override the defaults
present in script.py, ex:

```json
{
    "sd_api_pictures_tag_injection-manage_VRAM": 1,
    "sd_api_pictures_tag_injection-save_img": 1,
    "sd_api_pictures_tag_injection-prompt_prefix": "(Masterpiece:1.1), detailed, intricate, colorful, (solo:1.1)",
    "sd_api_pictures_tag_injection-secondary_positive_prompt": "nsfw, naked",
    "sd_api_pictures_tag_injection-secondary_negative_prompt": "young, old",
    "sd_api_pictures_tag_injection-sampler_name": "DPM++ 2M Karras"
}
```

will automatically set the `Manage VRAM` & `Keep original images` checkboxes and change the texts in `Prompt Prefix` and `Sampler name` on load as well as the seconday positive and negative prompt.
