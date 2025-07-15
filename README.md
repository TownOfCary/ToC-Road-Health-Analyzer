# Road Health Analyzer

> The Road Health Analyzer is an automated system designed to collect, process, and archive road condition data using GoPro cameras mounted on solid waste collection vehicles. It is the first pilot implementation of a broader 'Generalist AI Framework' for automating public sector tasks using AI, sensor data, and lightweight, reusable analysis pipelines.

### ✨ The Vision: Generalist AI Framework

Specialist AI models are **excellent** at feature detection, pattern recognition, and task automation. However, they require massive and highly curated datasets. A 'generalist' system uses modern multimodal LLMs like GPT 4o to enable acceptably good results without any additional training datasets; requiring only text instructions. Lower-stakes tasks (pothole detection, not cancer diagnostics) can absorb the reduced precision from a generalist model (still in the 90-95% range) without undermining core requirements at a fraction of the startup cost and lead time.

This pilot lays the groundwork for future municipal automation tools by enabling reuse of common data processing, organizing, and analysis tasks to engage multimodal LLMs like chatGPT 4o and Claude Sonnet. By varying the data inputs and rewriting natural language instructions, the same framework will enable much quicker deployment of new service automations or improvements:

- Pavement condition scoring
- Street sign inventory
- Parking utilization analysis
- Tree canopy estimation
- Deployed asset cataloging
- Sightline visibility review

And more as we explore

### 🌐 System Overview

> [Daily GoPro Recordings] → [Nightly Box Uploads] → [Automated Python Scripts] → [Frame Extraction + Analysis] → [Salesforce Work Orders + Box Archive] → [GeoJSON Output for GIS Integration]

### 🚀 Key Features

Fully automated from truck start-up to work order dispatch.

Records images based on distance traveled, beating video-based storage requirements by 97%

Archives raw footage and frames in Box for longevity and compliance

Embeds analysis results into Box-stored image metadata (in progress)

Automatically creates Salesforce Work Orders for high-confidence potholes

Saves GeoJSON metadata for future GIS integration

Tracks processed videos to avoid duplicates

### Core Modules

#### main.py

Handles the basic 'monitoring loop', which periodically checks Box for new videos (checking against already processed and logged videos).

#### processing.py

Handles the extraction of frames from a video (timelapse and video modes), extraction of telemetry metadata, and initial creation of a TelemetryObject for each frame.

#### utils.py

Contains the system messages, response format schemas, model information, and assistant ids for core analyzer types. For default 'road health analyzer' as of July 2025, use the 'batch assistant' when running and in batch mode. Be careful about using the other assistants, as they are likely outdated.

#### ai.py

Handles the processing of TelemetryObjects and frames through OpenAI, returning analysis results for each frame. Frames are processed in batches of 5 or 6 at the moment (5-6 images for each OpenAI request, AI is structured to respond for all X frames in one response).

#### box.py

Handles the Box integration and storage of frames, videos, and (separately) work order frames.

#### salesforce.py

Connects to Salesforce and handles the creation of AI Events. Also contains code to work with Work Orders and Work Tasks (since deprecated).

#### geospatial.py

Connects to Esri and enables street ownership checking.

#### run_headless.py

Launches the monitoring process, kicking off the whole shebang.

### ⚙️ System Requirements

Python 3.10+

Access to Box API with app-level permissions

Salesforce integration credentials (REST API)

A folder-based structure for incoming and outgoing videos

Camera with integrated GPS, Wi-Fi connectivity, and light scripting functionality.
Supported GoPros

- HERO5 - HERO11 (not tested)
- HERO13 (tested)

Hero 12 did not include GPS capability for cost and thermal management reasons, so it cannot be used for this system. Other camera brands and models (DJI, Meta, etc) can be used with adjustment to metadata extraction processes.

### 🔧 Setup

1. Install dependencies (NOTE: Run in Python 3.12 for geospatial functionality)

- I didn't make a requirements.txt (yet?) so good luck

2. Configure environment variables

- Create a .env file with Box, Salesforce, and OpenAI tokens.
  Check out utils.py for details on the assistant management variables, including system prompts and current instructions.

3. Run the system (headless mode)

- python run_headless.py

### 🔹 Salesforce Integration

Automatically creates Work Orders for potential road issues based on extracted frame analysis. Each Work Order is linked to Box-stored image assets.

### 🔹 Box Integration

Retrieves uploaded videos for processing

Saves processed frames and video archives

Future: adds telemetry_objects as custom metadata using Box Metadata Templates (currently stored as a separate JSON file)

### 🔹 GIS Integration (Next Phase)

GeoJSON output is already generated from frame metadata

Will integrate with ArcGIS Online or ArcGIS Enterprise to populate a shared layer

Used for spatial analysis, dashboards, and data validation

### 🔹 GoPro Integration

Uses GoPro Labs firmware to expand to beta features like QR-code-based setting and bootable scripts

GPS-connected cameras create photo timelapses with 1 frame captured for every X meters of travel distance.

### 📊 Timeline and Roadmap

#### January 2025

- Started, focusing on processing speed, cost efficiency, and automation of data pipelines.

#### February 2025

- Continued working on automation of pipelines, especially the Box integration.
- Expanded the AI connections to support flexible use in future projects (the framework bit)
- Made a web UI for control and awareness (major detour but it was a good learning experience)

#### March 2025

- Demoed the system to Public Works staff and management, securing support for a 5-vehicle pilot.
- Presented the project and the Generalist AI Framework to Cary executive team, securing additional support for the pilot before introducing it to Council.

#### Current (March 21, 2025)

- Installing cameras on trucks to start recording in 4 days.
- Logging staff ideas, suggestions, and requests for the next use-case to apply this framework towards.

#### Next (July 2025)

- Evaluating pilot-period data and performance
- Preparing and sharing results of the project with Council for approval and expansion to the full fleet
- Identifying high-impact use-case for expansion of the Framework

#### Future (July-December 2025)

- Improving the generalizability of the Framework to handle more input systems, instruction sets, and output methodologies
- Implementing incremental improvement via feedback loops and periodic fine-tuning of models

### ✉️ Contact / Contributing

This project is maintained by Connor McKinnis at the Town of Cary, NC, USA. For feedback, ideas, or partnership discussions, reach out to Connor McKinnis (connor.mckinnis@carync.gov). I know very little about what I'm doing, so feedback in simple terms is greatly appreciated.

(Internal use only at this stage. Contribution guidelines and license will be added post-pilot.)
