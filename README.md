# AI-Telegram-Educational-Video-Automation-Engine-Natural-Language-AI-Content-Generator-Publisher
<img width="1752" height="826" alt="the workflow" src="https://github.com/user-attachments/assets/a357c371-6bf2-41ab-87b4-f5a1ce941c81" />

## Project Goal
Build a smart AI-powered educational content engine where you can send a text message or voice note in Telegram (e.g. “Create a 60-second AI educational video explaining APIs” or speak the same), and the system automatically generates a complete faceless educational video with AI script, AI voice narration, subtitles, motion visuals.

## Problem
Creating educational AI videos manually takes time and involves many repetitive tasks such as researching topics, writing scripts, generating voiceovers, searching for visuals, editing videos, adding subtitles. This makes it difficult to consistently create and scale educational content production.

## Solution
The solution is to develop an n8n automation engine that converts Telegram text or voice notes into finished educational videos. The workflow uses Groq Whisper for transcription, Cerebras AI for scriptwriting, and Google TTS for narration, which are then combined with Pexels visuals and rendered via FFmpeg. 

## What Students Will Learn
- Connecting Telegram Bot with n8n (text + voice support)
- Speech-to-Text conversion workflows
- Advanced Prompt Engineering with Cerebras AI
- Natural language to AI educational content generation
- AI voice narration using Google-TTS
- Automated video rendering with FFmpeg
- Working with media APIs (Pexels/Pixabay)
- End-to-end AI automation architecture
- Workflow orchestration and error handling in n8n
- Building scalable AI-powered media systems

## Tech stack
- Automation: n8n
- Telegram command: Telegram Bot API
- Voice notes to text: Groq Whisper API
- Script generation: Cerebras AI
- Voice narration: Google Text-to-Speech API
- Visual assets: Pexels API
- Video rendering: FFmpeg
- Notification: Telegram Bot

## Project Workflow
Telegram → n8n → Transcription & Scripting → Media Generation → Rendering → Publishing
<img width="1106" height="737" alt="image" src="https://github.com/user-attachments/assets/e1cb6aab-1479-4ae6-b1cc-98ef245b1933" />

 

## Project Implementation Phases

### Phase 1: Input & Processing
Phase 1 focuses on building the communication foundation between Telegram and the n8n automation system. In this phase, students will learn how to create and connect a Telegram bot that can receive text messages and voice notes from users in real time. The goal is to establish a working input system where Telegram acts as the command center while n8n serves as the automation engine that processes incoming requests. By the end of this phase, students will understand how messaging platforms interact with automation workflows and how to trigger AI systems directly from Telegram.

#### Step 1: Telegram Trigger: The workflow starts when you send a text command or voice note to your bot.
- Open Telegram and search: BotFather
- Click the official BotFather, then send: /start
- Then send: /newbot
- BotFather will ask for your bot name: AI Video Automation Bot
- Then it will ask for username: growthengine_video_bot
- After this, BotFather will give you a bot token. Copy and save it

Now, let us go to the second part of this step

•	Go to your n8n dashboard. Create a new workflow. Name it: AI Telegram Educational Video Automation Engine
 <img width="769" height="178" alt="image" src="https://github.com/user-attachments/assets/e9a78908-cb72-4283-824f-281662d82792" />


•	Add a telegram trigger and select on message
 <img width="849" height="316" alt="image" src="https://github.com/user-attachments/assets/d76ed44e-63d3-4657-9167-1a6ff123b992" />


•	Inside the Telegram Trigger node, configure it with the token you just got.
 <img width="975" height="279" alt="image" src="https://github.com/user-attachments/assets/48b41953-a06f-49cf-a7ff-bdc475a283fe" />

•	Add the IF node after the Telegram trigger node to be able to differentiate voice from text. Name the if node Voice or Text

•	Configure the if node like this;

  - Value 1: {{ $json.message.voice ? 'VOICE_MESSAGE' : 'TEXT_MESSAGE' }}
  - Operation: is equal t0
  - Value 2: VOICE_MESSAGE
 <img width="975" height="454" alt="image" src="https://github.com/user-attachments/assets/409c41b2-4e2f-4854-b551-21fe0efac585" />


•	What this means is that TRUE = Voice notes and FALSE = Text message


#### Step 2: Speech-to-Text: If a voice note is received, the Groq Whisper API transcribes the audio into text.

•	Add Telegram “Get File” node from the TRUE output of your IF node. Name the node Get Voice Note File
 
<img width="850" height="325" alt="image" src="https://github.com/user-attachments/assets/a41fc31d-8625-459f-aa7d-342ec7168fb9" />











•	Get your Groq API Key to be used to convert voce to text
  - Go to: console.groq.com/keys
  - Sign in with GitHub/Google.
  - Click “Create API Key” and copy the key (starts with gsk_...)
 <img width="975" height="271" alt="image" src="https://github.com/user-attachments/assets/9c69a156-5008-4a4f-bd2f-93e4fbd3b6c7" />


•	After the Telegram Get a file node, add a Code Node (JS) node. This helps converts the extension of the audio message. Name the node Audio Conversion. Paste the below in the code node

```
for (const item of items) {
  if (item.binary && item.binary.data) {
    item.binary.data.fileName = 'voice.ogg';
    item.binary.data.mimeType = 'audio/ogg';
    item.binary.data.fileExtension = 'ogg';
  }
}
return items;
``` 
<img width="975" height="324" alt="image" src="https://github.com/user-attachments/assets/ae58d608-f7bf-4086-893c-7711020e3153" />



•	Search for HTTP node and add after the code node. Rename it to Voice to Text Conversion
  - Method: Post
  - URL: https://api.groq.com/openai/v1/audio/transcriptions
  - Authentication: None
<img width="744" height="303" alt="image" src="https://github.com/user-attachments/assets/2d027170-6f03-47c1-8286-6136326d6b19" />
 

•	Toggle on Send Header

•	Specify Header: Using fields below

•	Name: Authorization

•	Value: Bearer <API_KEY>
<img width="975" height="373" alt="image" src="https://github.com/user-attachments/assets/b5408d39-097f-47ca-a2f7-9c3ddcd7e5a3" />
 

•	Toggle on Send Body

•	Body Content Type: Form-Data

•	Under Body
  - Type: Form Data
  - Name: model
  - Value: whisper-large-v3
<img width="740" height="287" alt="image" src="https://github.com/user-attachments/assets/635f5534-5351-4f68-a8d4-510d4bdea603" />

 
•	Click add body field
  - Type: n8n Binary File
  - Name: file
  - Value: data
 <img width="700" height="210" alt="image" src="https://github.com/user-attachments/assets/c6ab8081-3edd-421f-a4c5-edd6df62a2e9" />


•	Click execute
 <img width="975" height="252" alt="image" src="https://github.com/user-attachments/assets/f87c3251-0d19-48f4-bb55-a045ae81165f" />


•	Add a set node after the http node and rename it to Extract Important Details (Voice)

•	Configure it like this:

Value 1
  - Name: chat_id
  - Operation: Number
  - Value: {{ $('Telegram Trigger').item.json.message.chat.id }}

Value 2
- Name: message
- Operation: String
- Value: {{ $json.text }}
 <img width="975" height="356" alt="image" src="https://github.com/user-attachments/assets/b330ce0e-cb29-4117-9edf-f27715511b78" />



•	Add a set node for the false part in the if node and rename it to Extract Important Details (Text)

•	Configure it like this:

Value 1
  - Name: chat_id
  - Operation: Number
  - Value: {{ $('Telegram Trigger').item.json.message.chat.id }}

Value 2
  - Name: message
  - Operation: String
  - Value: {{ $json.text }}
<img width="975" height="473" alt="image" src="https://github.com/user-attachments/assets/d75d5a0a-2f73-42cc-a871-2b948e33f08e" />
 





•	Now let us add a merge node and connect Extract Important Details (Text) and Extract Important Details (Voice) to it. Rename it to Merge Text and Voice

•	Configuration:
  - Mode: Append
  - Number of Inputs: 2
 <img width="975" height="347" alt="image" src="https://github.com/user-attachments/assets/43259f67-b6a2-4d04-b94e-b6a7af001914" />


#### Step 3: AI Scripting: The text is passed to Cerebras AI, which generates a structured educational script and a visual search query.
•	After your Merge Text and Voice node, add http node and rename it to AI Script Generation

•	Configure the HTTP node
  - Method: POST
  - URL: https://api.cerebras.ai/v1/chat/completions
  - Authentication: None
 <img width="975" height="186" alt="image" src="https://github.com/user-attachments/assets/9651fcfb-70fe-47c8-ab53-9a1e72dce12f" />


Turn on send header
  - Name: Authorization
  - Value: Bearer YOUR_CEREBRAS_API_KEY
<img width="765" height="222" alt="image" src="https://github.com/user-attachments/assets/fb0a77f6-b8d3-4ce9-8eba-87feb24b3b4c" />
 

- Toggle on send body
- Specify header: JSON
- Paste the below:
```
{
  "model": "llama3.1-8b",
  "messages": [
    {
      "role": "system",
      "content": "You are a professional educational video production AI. Your job is to receive a topic request and produce a complete structured video production plan for an automated video pipeline. You MUST return only raw valid JSON. Do not use markdown. Do not explain anything. Do not add code blocks. Every field is mandatory."
    },
    {
      "role": "user",
      "content": "TOPIC REQUEST: {{ $json.message }}\n\nCreate a complete educational video production plan.\n\nDURATION RULES:\n- If the user mentions a duration, use that duration.\n- If no duration is mentioned, default to 60 seconds.\n- The narration must be written to fit the selected duration.\n- Use normal speaking speed of about 2.2 words per second.\n- For a 60-second video, full_narration should be about 120 to 140 words.\n- For a 30-second video, full_narration should be about 60 to 75 words.\n- For a 2-minute video, full_narration should be about 240 to 280 words.\n- Do not make narration too short for the video duration.\n- Do not make narration too long for the video duration.\n\nSCENE TIMING RULES:\n- Break the narration into scenes.\n- Each scene duration must match the amount of narration in that scene.\n- Use this timing formula: scene_duration_seconds = round(number_of_words_in_scene / 2.2).\n- Short simple scenes should be 5 to 8 seconds.\n- Longer explanation scenes should be 10 to 18 seconds.\n- All scene duration_seconds must add up exactly to duration_seconds.\n- The difference between total narration speaking time and total scene duration must not be more than 3 seconds.\n- Subtitles must follow the same timing as scenes.\n- subtitle start_time and end_time must match scene boundaries exactly.\n\nIMPORTANT:\n- Every scene narration must be part of the full_narration.\n- full_narration must equal all scene narrations joined together in order.\n- visual_keyword must match what is being said in that scene.\n- subtitle_text must be short, maximum 10 words.\n\nReturn ONLY a valid JSON object with exactly this structure:\n\n{\n  \"title\": \"A clear engaging YouTube video title\",\n  \"description\": \"A 2-3 sentence YouTube description summarising what viewers will learn\",\n  \"tags\": [\"tag1\", \"tag2\", \"tag3\", \"tag4\", \"tag5\"],\n  \"duration_seconds\": 60,\n  \"estimated_narration_words\": 130,\n  \"estimated_speaking_seconds\": 59,\n  \"visual_search_query\": \"3-4 word Pexels search keyword\",\n  \"voice\": {\n    \"full_narration\": \"Complete word-for-word narration for the entire video. It must match the target duration closely.\",\n    \"tone\": \"professional and engaging\",\n    \"pace\": \"medium\"\n  },\n  \"scenes\": [\n    {\n      \"scene_number\": 1,\n      \"duration_seconds\": 7,\n      \"narration\": \"Exact words spoken in this scene.\",\n      \"subtitle_text\": \"Short subtitle text\",\n      \"visual_keyword\": \"3-word Pexels search term\"\n    },\n    {\n      \"scene_number\": 2,\n      \"duration_seconds\": 12,\n      \"narration\": \"Exact words spoken in this scene.\",\n      \"subtitle_text\": \"Short subtitle text\",\n      \"visual_keyword\": \"3-word Pexels search term\"\n    }\n  ],\n  \"subtitles\": [\n    {\n      \"start_time\": 0,\n      \"end_time\": 7,\n      \"text\": \"Short subtitle text\"\n    },\n    {\n      \"start_time\": 7,\n      \"end_time\": 19,\n      \"text\": \"Short subtitle text\"\n    }\n  ]\n}"
    }
  ],
  "temperature": 0.2
}
``` 
<img width="975" height="430" alt="image" src="https://github.com/user-attachments/assets/0f7870b3-1eb7-4c95-a16a-b3c965e74cbc" />


•	After the http AI Script Generation node, add a code node and name it Parse AI Script Output. Paste the below;
```
const raw = items[0].json.choices[0].message.content;

// Clean the string in case Cerebras wraps it in markdown
const cleaned = raw
  .replace(/```json/g, '')
  .replace(/```/g, '')
  .trim();

const parsed = JSON.parse(cleaned);

return [{
  json: {
    // ── METADATA ──────────────────────────
    title:                parsed.title,
    description:          parsed.description,
    tags:                 parsed.tags,
    duration_seconds:     parsed.duration_seconds,
    visual_search_query:  parsed.visual_search_query,

    // ── VOICE (goes to Google TTS) ─────────
    full_narration:       parsed.voice.full_narration,
    voice_tone:           parsed.voice.tone,
    voice_pace:           parsed.voice.pace,

    // ── VIDEO (goes to Pexels + FFmpeg) ────
    scenes:               parsed.scenes,
    subtitles:            parsed.subtitles,

    // ── PASS THROUGH ──────────────────────
    chat_id:              items[0].json.chat_id  
      || $('Merge Text and Voice').item.json.chat_id
  }
}];
``` 

### Phase 2: Asset Generation
Phase 2 focuses on transforming the AI-generated production plan into actual media assets that will be used to create the final educational video. In this phase, the system converts the generated narration script into realistic AI voice audio using Google Text-to-Speech, while simultaneously retrieving relevant background visuals from media platforms like Pexels based on the AI-generated visual keywords. The goal of this phase is to automatically produce all the core multimedia components required for video rendering, including narration audio, video clips, images, and visual references, preparing the workflow for the final assembly and rendering stage.

#### Step 1: Voice Narration: The script is sent to the Google Text-to-Speech API to create a high-quality audio file.

•	Open your google console
```
https://console.cloud.google.com/
```
 <img width="975" height="368" alt="image" src="https://github.com/user-attachments/assets/b9eaf020-022a-46ab-a6b1-fefdf392f289" />



•	At the top of google console, select project and click on new project. Name the project AI Video Automation Engine and click create
 <img width="975" height="342" alt="image" src="https://github.com/user-attachments/assets/425f2d83-1f40-471c-9fbc-0438e91412f3" />


•	At the top search bar in Google Cloud Console, search: Text-to-Speech API and enable it
 <img width="975" height="290" alt="image" src="https://github.com/user-attachments/assets/73e9fd66-22f9-4683-9c12-c01035eb82af" />


•	On the left sidebar, go to APIs and Services, click credentials and then create credentials and select service account
 <img width="975" height="341" alt="image" src="https://github.com/user-attachments/assets/dcaa79cb-57ae-47c9-9ce8-13bc185d69dd" />






•	Use n8n-google-tts as the service name and click done
 <img width="1035" height="346" alt="image" src="https://github.com/user-attachments/assets/8958f431-1aaa-4ba7-adec-4df10d73150d" />



•	Click the service account you just created, click on key, select add key and then create a new key
 <img width="975" height="342" alt="image" src="https://github.com/user-attachments/assets/8931d5e1-53cc-442c-8bac-9b6662d01b08" />


•	Select JSON and then click create
 <img width="975" height="419" alt="image" src="https://github.com/user-attachments/assets/d59414e4-0254-465d-98dd-70cbe983256e" />


•	A JSON file would immediately download that contains your key

Now, let’s go back to our n8n console

•	Add a http node after the parse AI script output node. Rename it to Generate Voice Narration

•	Configure the node like this:
  - Method: POST
  - URL: https://texttospeech.googleapis.com/v1/text:synthesize
  - Authentication: Predefined Credential Type
  - Credential Type: Google Service Account
  - For credential, select new and fill the details
 <img width="956" height="494" alt="image" src="https://github.com/user-attachments/assets/cf8527c8-f579-4046-bd00-9c45ea1237ff" />



In the credential part, toggle on Set up use in HTTP Request node and then paste the below in scopes
 <img width="975" height="243" alt="image" src="https://github.com/user-attachments/assets/1d4ac4ac-0142-458f-a320-954866315c48" />






Toggle on Send header
  - Name: Content-Type
  - Value: application/json
<img width="790" height="378" alt="image" src="https://github.com/user-attachments/assets/39b83c4f-d5d0-4787-a7f9-eedaa37cc3bc" />
 

Toggle on Send body

- Body Content Type: JSON
- Specify Body: Using JSON
- Paste the below;
```
{
  "input": {
    "text": "{{ $json.full_narration }}"
  },
  "voice": {
    "languageCode": "en-US",
    "name": "en-US-Neural2-F",
    "ssmlGender": "FEMALE"
  },
  "audioConfig": {
    "audioEncoding": "MP3",
    "speakingRate": 1.0,
    "pitch": 0
  }
}
```
<img width="975" height="321" alt="image" src="https://github.com/user-attachments/assets/b477be78-16c1-44e2-9c53-e92d5e3ea5fc" />
 
•	Add a convert to file node (Move base64 string to file) after the http node and rename it to Create Narration Audio File

•	Under Base64 Input field, type audioContent
 <img width="975" height="270" alt="image" src="https://github.com/user-attachments/assets/e7ffa0d8-90ed-4e54-8c46-bf4ae2d67164" />


#### Step 2: Visual Retrieval: n8n uses the Pexels API to find background video clips or images that match the AI's script.

•	Go to https://www.pexels.com/api/, click get started and log in (Log in with your Gmail)
 <img width="975" height="356" alt="image" src="https://github.com/user-attachments/assets/deb97a98-d6ea-4f94-8a2e-ff6c098714db" />


•	Go to your profile, and select image and video API
 <img width="975" height="342" alt="image" src="https://github.com/user-attachments/assets/529d4976-7678-4341-8d3f-95cf644016e2" />

•	Click Your API Key
  - Project Name: AI Telegram Educational Video Automation Engine
  - Project Category: AI
  - Explanation: We are building an AI-powered educational video automation system using n8n. The platform automatically generates educational videos from Telegram text or voice commands. Pexels videos and images will be used as background visuals for AI-generated educational content before rendering final videos with FFmpeg and publishing to YouTube.
 <img width="991" height="571" alt="image" src="https://github.com/user-attachments/assets/5a474fb7-1eed-4fd2-a4bb-880f3b0b0dcd" />


•	Once you click generate API Key, the key automatically displays. Save the API Key


Let’s go back to our n8n workflow

•	Add a http node after the create narration audio file and rename it to Retrieve Pexels Visuals

•	Configure it like this
  - Method: GET
  - URL: https://api.pexels.com/videos/search
  - Authentication: None
 <img width="625" height="267" alt="image" src="https://github.com/user-attachments/assets/97dfd6c9-6b8a-49f5-809c-c51db55deab6" />


Toggle on Send Query Parameters

Query Parameter 1
  - Name: query
  - Value: {{ $json.visual_keyword }}

Query Parameter 2
  - Name: per_page
  - Value: 3

Query Parameter 3
  - Name: orientation
  - Value: landscape
 <img width="975" height="507" alt="image" src="https://github.com/user-attachments/assets/b47c7072-37d2-4a4b-8b33-5a028b43fd9f" />


Toggle on Send Header
  - Name: Authorization
  - Value: YOUR_PEXELS_API_KEY
 <img width="833" height="389" alt="image" src="https://github.com/user-attachments/assets/9eeaa0ce-bb5c-4da4-a554-1eeea6c6ee67" />


•	We need to extract the actual downloadable video URL from the Pexels response. Add a Code node after Retrieve Pexels Visuals. Rename it to Extract Best Pexels Video URL and paste the below:
```
return items.map(item => {
  const scene = item.json;
  const videos = scene.videos || [];

  if (!videos.length) {
    return {
      json: {
        ...scene,
        error: 'No Pexels video found'
      }
    };
  }

  const sceneDuration = scene.duration_seconds;

  const selectedVideo =
    videos.find(v => v.duration >= sceneDuration)
    || videos[0];

  const bestFile =
    selectedVideo.video_files.find(file => file.quality === 'hd' && file.width >= 1280)
    || selectedVideo.video_files.find(file => file.quality === 'hd')
    || selectedVideo.video_files[0];

  return {
    json: {
      scene_number: scene.scene_number,
      duration_seconds: sceneDuration,
      visual_keyword: scene.visual_keyword,
      narration: scene.narration,
      subtitle_text: scene.subtitle_text,

      pexels_video_url: bestFile.link,
      original_video_duration: selectedVideo.duration,

      trim_or_loop_to_seconds: sceneDuration,

      title: scene.title,
      description: scene.description,
      tags: scene.tags,
      chat_id: scene.chat_id
    }
  };
});
``` 
<img width="975" height="308" alt="image" src="https://github.com/user-attachments/assets/b2f6be61-73c7-4d67-a9f5-4415a42be91b" />

•	Now, let us download the actual video. Add a Http node after the code node and rename it to Download Pexels Video

•	Configure it like this;
  - Method: Get
  - URL: {{ $json.pexels_video_url }}
<img width="975" height="314" alt="image" src="https://github.com/user-attachments/assets/327f8fd0-97f3-48bb-8bfb-2316f31d643e" />
 

•	Add the code node after Create Narration Audio File and before Retrieve Pexels Visuals. Rename it to Split Scenes for Pexels and paste the below
```
const parsed = $('Parse AI Script Output').item.json;

return parsed.scenes.map(scene => {
  return {
    json: {
      scene_number: scene.scene_number,
      duration_seconds: scene.duration_seconds,
      narration: scene.narration,
      subtitle_text: scene.subtitle_text,
      visual_keyword: scene.visual_keyword,

      title: parsed.title,
      description: parsed.description,
      tags: parsed.tags,
      full_narration: parsed.full_narration,
      total_duration_seconds: parsed.duration_seconds,
      subtitles: parsed.subtitles,
      chat_id: parsed.chat_id
    }
  };
});
``` 

### Phase 3: Assembly & Delivery
Phase 3 focuses on transforming all the generated media assets into a complete, publish-ready educational video and automatically delivering it to the final audience platform. In this phase, the narration audio, Pexels visuals, subtitles, and scene timing generated in earlier phases are assembled together using FFmpeg to produce a fully rendered MP4 educational video. After rendering, the video is sent back to the telegram user
Step 1: Video Rendering: All components (audio, visuals, and generated subtitles) are sent to FFmpeg, which renders the final MP4 file.

•	Let us download FFmpeg in our terminal. SSH into your terminal
```
ssh root@YOUR_SERVER_IP
```
•	Update the server. Run;
```
sudo apt update
``` 
<img width="975" height="193" alt="image" src="https://github.com/user-attachments/assets/eba66324-3757-4cc4-9252-e71cd6bed8c9" />



•	Install FFmpeg. Run;
```
sudo apt install ffmpeg -y
``` 
<img width="975" height="243" alt="image" src="https://github.com/user-attachments/assets/4508a5de-4f98-41d9-911d-89e6cbe10d0a" />


•	Verify installation, run;
```
ffmpeg -version
``` 
<img width="975" height="276" alt="image" src="https://github.com/user-attachments/assets/0f725ea2-7f35-4a98-a032-ba766a8de7a9" />

•	Run the below commands on your vps;
```
mkdir -p /home/n8n-files
sudo chmod -R 777 /home/n8n-files
```

•	Run these also;
```
export N8N_RESTRICT_FILE_ACCESS_TO=/home/n8n-files
pm2 restart 4 --update-env
pm2 save
```

Now, let us go back to our n8n workflow

•	After Download Pexels Video, add SSH (Execute a command) node and rename it to Prepare Scene Video

•	In the command configuration, paste the below;
```
ffmpeg -y -stream_loop -1 -i "{{ $json.pexels_video_url }}" -t {{ $json.original_video_duration }} -vf "scale=1280:720:force_original_aspect_ratio=increase,crop=1280:720,fps=30" -an "/home/n8n-files/scene-{{ $itemIndex + 1 }}.mp4"
``` 
<img width="975" height="421" alt="image" src="https://github.com/user-attachments/assets/06e2485b-18fc-4c98-bda0-063f8bce9cb6" />

•	After Prepare Scene Video, add SSH (Execute a command) node and rename it to Join Scene Videos

•	In the command configuration, paste the below;
```
printf "file '/home/n8n-files/scene-1.mp4'\nfile '/home/n8n-files/scene-2.mp4'\nfile '/home/n8n-files/scene-3.mp4'\nfile '/home/n8n-files/scene-4.mp4'\n" > /home/n8n-files/video-list.txt && ffmpeg -y -f concat -safe 0 -i /home/n8n-files/video-list.txt -c copy /home/n8n-files/silent-final-video.mp4
``` 
<img width="975" height="351" alt="image" src="https://github.com/user-attachments/assets/ad5f81d5-3d11-49e9-a3f0-d031953dcb99" />

•	Next, add a Code node after Join Scene Videos to reduce it to only 1 item. Name it Keep One Final Video Item and paste the below;
```
return [
  {
    json: {
      silent_video_path: "/home/n8n-files/silent-final-video.mp4"
    }
  }
];
```
<img width="956" height="288" alt="image" src="https://github.com/user-attachments/assets/688e4d93-b5e9-4e9a-b8c8-4b7fb9a3fe8e" />


•	After Keep One Final Video Item, add a code node. Rename it to Bring Narration Audio Forward and paste the below
```
const narrationItem = $('Create Narration Audio File').item;

return [
  {
    json: {
      ...items[0].json,
      final_video_path: "/home/n8n-files/final-ai-video.mp4"
    },
    binary: {
      data: narrationItem.binary.data
    }
  }
];
``` 
<img width="975" height="354" alt="image" src="https://github.com/user-attachments/assets/a383bf72-93c4-43ac-965d-30f3e01f744c" />

•	Add an SSH node after Bring Narration Audio Forward and name in Upload the Voice Narration

•	Configure it like this:
  - Resource: File
  - Operation: Upload
  - Input Binary Field: data
  - Target Directory: /home/n8n-files
  - Add Options, File Name: narration.mp3
 <img width="948" height="348" alt="image" src="https://github.com/user-attachments/assets/53804dee-6bcc-4ae2-8186-01f196c161fc" />


•	Add another SSH node and name it Merge Video and Audio and then paste the below in the command:
```
ffmpeg -y -i /home/n8n-files/silent-final-video.mp4 -i /home/n8n-files/narration.mp3 -map 0:v:0 -map 1:a:0 -c:v libx264 -c:a aac -t $(ffprobe -v error -show_entries format=duration -of default=nw=1:nk=1 /home/n8n-files/narration.mp3) /home/n8n-files/final-ai-video.mp4
``` 
<img width="975" height="279" alt="image" src="https://github.com/user-attachments/assets/1504ea65-1b87-4adf-a554-07bcdf757044" />


•	Now we need to bring the final video back into n8n as binary, so you can see it, download it. After Merge Video And Audio, add: SSH Node and rename it to Download Final Video From Server. Choose the download operation and paste the below in the path
```
/home/n8n-files/final-ai-video.mp4
``` 
<img width="975" height="316" alt="image" src="https://github.com/user-attachments/assets/4a2c9f72-9d93-404b-8d7d-8b6e471c962a" />


•	After Download Final Video From Server, add: Telegram Node (send a video) and rename it Send Final Video to Telegram

•	Configure
  - Chat ID: {{ $('Parse AI Script Output').item.json.chat_id }}
  - Toggle on Binary
 <img width="950" height="434" alt="image" src="https://github.com/user-attachments/assets/ef38853e-74f3-4312-aa5f-8e3803d1f157" />


### Conclusion
This project demonstrates how AI automation can transform a simple Telegram text or voice command into a fully generated educational video without manual editing. By combining n8n, Groq Whisper, Cerebras AI, Google Text-to-Speech, Pexels, and FFmpeg, the system automatically handles scripting, narration, visual retrieval, video rendering, and final delivery back to Telegram. Through this project, students gain practical experience in AI workflow orchestration, API integration, media automation, and production-level AI system design while building a real-world intelligent content generation engine.
