# Vertex AI and AI Services: ACE Exam Study Guide (2026)

## 1. Vertex AI (High-Level Overview)

Vertex AI is Google Cloud's unified platform for machine learning (ML). It brings together all the services for building, deploying, and scaling ML models.

### Key Concepts for the ACE Exam:

- **Unified Platform:** Integrates AutoML and Custom Training into a single workflow.
- **AutoML:** A no-code/low-code approach for creating high-quality models for images, video, text, and tabular data.
- **Custom Training:** For data scientists who want to use their own ML frameworks (TensorFlow, PyTorch, Scikit-learn).
- **Model Garden:** A curated collection of first-party, open-source, and third-party models (including Gemini) that can be deployed quickly.
- **Endpoints:** Used for online (real-time) predictions. Models must be deployed to an endpoint to receive traffic.
- **Batch Prediction:** Used for large datasets where real-time response is not required (e.g., overnight processing).

### 2026 Exam Focus: Generative AI

- **Gemini Models:** Integrated directly into Vertex AI (1.5 Pro, 1.5 Flash). Gemini can be accessed via Vertex AI Studio for prototyping and then deployed as part of an application.
- **Vertex AI Agent Builder:** A managed service for building AI-powered search and chat interfaces (formerly Search and Conversation) with minimal coding.
- **Gemini for Google Cloud:** The AI-powered assistant available across the Google Cloud Console for troubleshooting, coding, and management.

### IAM Roles for Vertex AI:

- roles/aiplatform.admin: Full access to all Vertex AI resources.
- roles/aiplatform.user: Permission to use Vertex AI features (create jobs, deploy models).
- roles/aiplatform.viewer: Read-only access to Vertex AI resources.

## 2. Vision API

The Cloud Vision API allows developers to integrate vision detection features within applications, including image labeling, face and landmark detection, optical character recognition (OCR), and tagging of explicit content.

### Key Features:

- Label Detection: Identifies objects, locations, and activities in an image.
- OCR (Optical Character Recognition): Detects and extracts text from images.
- Object Localization: Identifies where objects are located within an image and provides a bounding box.
- Safe Search Detection: Detects explicit content (adult, medical, violence).

### Usage Scenario:

If you need to identify objects or text in images without building a custom model, use the Vision API. If the pre-trained API is not accurate enough for your specific industry (e.g., identifying specific defective parts in a factory), use Vertex AI AutoML Vision.

## 3. Speech-to-Text (STT)

Cloud Speech-to-Text enables developers to convert audio to text by applying powerful neural network models.

### Recognition Types:

- Synchronous Recognition: Used for short audio files (less than 1 minute). The user waits for the response.
- Asynchronous Recognition: Used for long audio files (up to 480 minutes). The results are retrieved later.
- Streaming Recognition: Used for real-time audio (e.g., live captions or voice assistants).

### Configuration and Integration:

- Storage: Large audio files for asynchronous processing must be stored in a Cloud Storage (GCS) bucket.
- Language Support: Supports over 125 languages and variants.
- Accuracy Features: Can be improved using Speech Adaptation (providing hints for specific words or phrases).

## 4. Translation API

Cloud Translation API makes it easy to translate text into thousands of language pairs.

### Basic vs. Advanced:

- Cloud Translation - Basic (v2): Simple, fast, and suitable for basic text translation.
- Cloud Translation - Advanced (v3): Supports more complex features like Glossaries (ensuring specific industry terms are translated correctly) and Batch Translation.

### 2026 Exam Gotchas:

- Adaptive Translation: A newer feature using LLMs (like Gemini) to provide context-aware translations that match the tone and style of the source text.
- Detection: The API can automatically detect the source language if it is not provided.

## 5. Summary Cheat Sheet for AI Selection

- Need to extract text from a photo? Vision API (OCR).
- Need to transcribe a 2-hour meeting recording? Speech-to-Text (Asynchronous).
- Need to translate technical manuals with specific terminology? Translation API Advanced (Glossaries).
- Need to build a custom model for your specific company data? Vertex AI (AutoML or Custom Training).
- Need to build a GenAI chatbot with your own documents? Vertex AI Agent Builder.
