# Developer note: Due to the sponsor owning the rights to the code I have linked the readme from the project here and a GitHub pages link so users that visit my GitHub page can still use my WebApp.

# Fall Risk Awareness Project

## What is this project?
This project is a website designed to run on a mobile web browser. It utilizes your phone's camera to scan items in your room that are either fall risks or essential items to reduce your risk of falling, such as a walker. This application aims to mitigate the significant number of falls that occur annually, a figure that is projected to continue growing, especially among seniors. The primary goal of this website is to raise awareness about reducing fall risks at home. It targets seniors at risk of falling (anyone over 65) as well as in-home caregivers.

## How to run this project?

### Online Access
- **Step 1:** Visit our GitHub Pages URL: [https://gvsucis.github.io/w24-corewell/](https://gvsucis.github.io/w24-corewell/)

### Running Locally
- **Step 1:** Download the GitHub files from the main branch onto your computer.
- **Step 2:** Ensure that you have a web browser installed on your computer with JavaScript enabled (e.g., Chrome, Firefox, Edge, Safari). Then, with the GitHub files in a folder, open the `index.html` file with that web browser. The website should open and be usable. Or use a website deployer like [Live Server](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer) by Ritwick Dey, which is a VS Code extension. With the github code open in a folder on VS Code right click the index.html file with Live Server extenstion installed and press "Open with Live Server".

## What do the files do?

### Homepage
- `index.html` - The main homepage of the website.
- `style.css` - The CSS file for `index.html`.
- `main.js` - The JavaScript code for the `index.html` file. This handles the model detection and saves the data to local storage.

### Model
- `fallriskmodel.tflite` - Our trained TensorFlow Lite model, designed to run on web browsers and called by the `mediapipe.js` file.
- `mediapipe.js` - Google's MediaPipe JavaScript code that allows us to use the TensorFlow model and run WebAssembly, meaning the model runs on-device instead of communicating through a server for detection results.

### Results Page
- `results.html` - The HTML file for the results page.
- `results.js` - The JavaScript file for `results.html`.
- `results-style.css` - The CSS file for `results.html`.

### Questionnaire Page
- `questionnaire.html` - The HTML file hosting the CDC fall risk questionnaire.
- `questionnaire-style.css` - The CSS file for `questionnaire.html`.

## Model Training Overview

### Dataset Preparation
The images used for training were sourced and annotated using [Roboflow](https://roboflow.com/). A specialized image database named "FallRisk" was created to facilitate this process. You can view and download the dataset from [Roboflow Universe](https://universe.roboflow.com/fall-risk-detection/fallrisk-version-2).

### Model Training Process
The model training was conducted with the aid of [Google's MediaPipe Model Maker](https://developers.google.com/mediapipe/solutions/model_maker), leveraging a pre-existing model initially trained on the [COCO dataset](https://cocodataset.org/#home). This base model was further trained to recognize only the following 11 classes critical for fall risk detection:
- Walker
- Table
- Rug
- Lamp
- Handrail
- Flashlight
- Cord
- Chair
- Cane
- Bed

Google Colab was the chosen platform for training, allowing access to powerful AI GPUs at a low cost. Specifically, the NVIDIA A100 GPU was utilized to experiment with various batch sizes and learning rates, optimizing for the best possible outcomes. We created this [Google Colab notebook](https://colab.research.google.com/drive/115fjNcr4eI93JOV3vrej_CgU43bBv_iJ?usp=sharing) linked here for the training process.

### Model Integration and Deployment
The result of the training process was the `fallrisk.tflite` model. This model was subsequently integrated with MediaPipe.js through a custom `main.js` script, enabling its application in fall risk detection projects.

## How Does the Model Work, Without Uploading or Communicating to a Server?

The model operates entirely on the user's device, leveraging [WebAssembly](https://developer.mozilla.org/en-US/docs/WebAssembly) and JavaScript to make use of the device's hardware. This setup is enhanced by a pre-trained, lightweight image recognition model, ensuring that all processing is conducted on-device. Upon accessing the website, the user's device downloads a roughly 10MB file—the image recognition model—which then runs locally to detect objects within the user's video feed.

Here is a [resource from Google](https://developers.googleblog.com/2020/01/mediapipe-on-web.html) explaining the process in detail. According to Google:

"In order to maximize portability, we use Emscripten to directly compile all of the necessary C++ code into WebAssembly, which is a special form of low-level assembly code designed specifically for web browsers. At runtime, the web browser creates a virtual machine in which it can execute these instructions very quickly, much faster than traditional JavaScript code.

We also created a simple API for all necessary communications back and forth between JavaScript and C++, to allow us to change and interact with the MediaPipe graph directly from JavaScript. For readers familiar with Android development, you can think of this as a similar process to authoring a C++/Java bridge using the Android NDK.

Finally, we packaged up all the requisite demo assets (ML models and auxiliary text/data files) as individual binary data packages, to be loaded at runtime. And for graphics and rendering, we allow MediaPipe to automatically tap directly into WebGL so that most OpenGL-based calculators can 'just work' on the web." Although they are explaining how their graph website works that shows the graph of how a mediapipe model is working. It is very similar to how their Object detection models work on the web.
