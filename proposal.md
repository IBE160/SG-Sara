## Case Title

StudyBuddy AI - Your Smart Learning Assistant

## Background

Students face challenges efficiently processing large volumes of lecture and reading materials. There is a lack of intuitive tools that leverage AI to generate structured study aids supporting active learning and self-testing.

## Purpose

The purpose of the StudyBuddyapp is to assist students in processing study materials more effectively by using AI to create summaries, flashcards, and quizzes from uploaded notes and readings. It supports active learning through repetition and self-testing and demonstrates AI's practical application in higher education.

## Target Users

- University and college students who want to process lecture notes and readings efficiently.

## Core Functionality

Must Have (PoC):
-Feature 1: Upload text sources: PDF and DOCX (max 10 MB; text-only PDFs supported in PoC).
-Feature 2: AI outputs per upload: one summary (short or medium), 10 flashcards (Q/A), and 6 multiple-choice questions (4 options, 1 correct).
-Feature 3: Review & save: users can view, save, and re-open generated content after login.
-Feature 4: Authentication: secure sign-in; user data scoped to the logged-in user only.
-Feature 5: Online requirement: AI generation requires internet connectivity (cloud inference).

### Nice to Have (Optional Extensions)

-Feature 5: Option to share or collaborate on generated content with other users
-Feature 6: Adjustable detail level and difficulty of generated questions
-Feature 7: Option to choose output language (e.g., English or Norwegian)
-Feature 8: Personalized quiz feed
-Feature 9: Learning analytics showing student strengths and weaknesses.  
-Feature 10: Tailored explanations for wrong quiz answers.  
-Feature 11: Difficulty adjustment according to the level of the student  
-Feature 12: Offline access to previously saved content.

## Feature Activation - Flashcard Generation

- **Actor:** Student user
- **Trigger:** User clicks “Generate flashcards” button after upload or summary generation
- **Goal:** Provide a set of flashcards to support active recall and study

## Data Requirements

- Data entity 1: User profiles: identifiers, emails, display name. No passwords stored by the application (authentication handled by Firebase)
- Data entity 2: Uploaded file metadata: file type, language, subject, course code
- Data entity 3: Generated content: summaries, flashcards, quiz questions and answers, key terms.
- Data entity 4: Preferences: preferred language, detail level, content format preferences

## User Stories (Optional)

1. As a student, I want to upload my lecture notes so that I can get an AI-generated summary of the key points.
2. As a student, I want the system to create flashcards so that I can review important concepts before exams.
3. As a student, I want to save and reuse my quizzes so that I can test my knowledge multiple times.
4. As a student, I want clear feedback if my file cannot be processed so I understand what to fix"
5. As a user, I want to delete my uploaded content to maintain privacy"

## User Flow Overview

User Registration and Login

1. Register or log in via email/social auth.
2. Access dashboard on successful login.

File Upload

1. Select and upload supported file (PDF/DOCX).
2. System validates file type and size.
3. Notify user of upload success or errors.

Text Extraction

1. Backend extracts text from uploaded file.
2. Notify user when extraction completes.

Summary Generation

1. User triggers summary generation or it starts automatically.
2. AI processes text and returns a concise summary.
3. Display summary with save/edit options.

Flashcard Creation

1. User generates flashcards from summary/text.
2. Flashcards shown interactively; user can review and save.

Quiz Generation and Completion

1. User starts quiz based on flashcards.
2. Answer MCQs, get immediate feedback and explanations.
3. Quiz results saved for progress tracking.

Review and Study Management

1. Access history of uploads and generated content.
2. Schedule reviews or share study aids if enabled.

Error Handling

1. Clear feedback on invalid files or failures.
2. Retry mechanisms for network or AI timeouts.

## Technical Constraints

- AI generation online only; no OCR for scanned documents during PoC.
- Target upload size up to 10 MB text-based PDFs and DOCX.
- Performance goals: median response under 5 seconds; 90th percentile under 12 seconds.
- GDPR-compliant privacy with encrypted data and easy deletion.

## Technology Stack (PoC)

- Backend: FastAPI (Python) — lightweight and fast API framework for building the backend services.

- User Interface: Simple web UI using FastAPI's built-in HTML templates; no complex frontend frameworks (React, Vue, Angular) in PoC to keep it simple.

- AI Service: Google Gemini as the main AI engine with OpenAI GPT-4 API as backup. This ensures reliable AI content generation.

- Authentication & Database: Firebase Authentication for user sign-in, Firestore for storing user data and generated content.

- Document Parsing: Use Python libraries for extracting text:

  - pdfminer.six for PDFs with text
  - python-docx/docx2txt for Word documents
  - pypdf for splitting documents and metadata
  - Text normalization using regular expressions and ftfy
  - Note: OCR for scanned documents is not included in this PoC.

- Validation & Testing: Use Pydantic for strict data validation and lightweight unit tests to ensure reliability of parsing and API flows.

## Architecture Overview

- User interacts with a simple web UI (tool to be chosen).

- Web UI calls FastAPI backend endpoints: /upload, /generate, /content/{id}.

- Backend sends requests to LLM (Google Gemini) hosted in the cloud to generate summaries, flashcards, and quizzes.

- Generated results are stored persistently in Firestore for later retrieval.

- API follows a small REST design returning JSON responses with clear error messaging and simple retry logic.

- Behavior: AI generation requires internet access; offline viewing of previously saved content may be supported if time permits.

## Data Model

- Users: { uid, email, name, createdAt }

- Files: { fileId, uid, name, type, createdAt, meta }

- Content: { contentId, uid, fileId, kind (summary|flashcards|mcq), payloadJSON, createdAt }

The system uses a document-oriented NoSQL database model, suited to storing per-user content separately. Complex data warehouse schemas like star schema are not applicable here.

## Success Criteria

- Criterion 1: Users can upload files and reliably receive summaries, flashcards, and quizzes with at least 95% completing the full pipeline (upload → extract → generate → save) without critical errors.
- Criterion 2: Generated content must be accurate, relevant, and parsed as valid JSON at least 90% of the time.
- Criterion 3: Performance targets: median response time under 5 seconds, 90th percentile under 12 seconds for inputs up to 3,000 tokens.
- Criterion 4: Mini user test (3–5 users) feedback shows at least 85% rate summaries and flashcards as useful or very useful.
- Criterion 5: New users can upload and generate materials within 2 minutes without instructions.
- Criterion 6:Data is stored securely, accessible post-login, and user privacy is ensured through enforced authentication, data isolation, request-based deletion, and GDPR compliance.
- Criterion 7: The interface is intuitive, responsive, and works smoothly on both desktop and mobile.
- Criterion 8: Quiz feedback helps users identify learning strengths, weaknesses, and provides study recommendations.
- Criterion 9: The AI performs consistently across subjects and languages, with optional robustness to handle different topics without prompt changes.
- Criterion 10: The system exhibits zero critical crashes across functional test suites.

## Timeline / Milestones (6 Weeks PoC)

- Week 1: Project setup, authentication, basic UI.
- Week 2: File upload and text extraction.
- Week 3: Summary generation.
- Week 4: Flashcards and quizzes.
- Week 5: Usability testing and incremental improvements.
- Week 6: Final delivery with demo and documentation.

## Risks and Mitigations

- Rate limits and latency spikes: fallback to cached short summaries.
- Version control: Git branching to prevent overwrite errors.
- Team coordination: weekly standups and clear role definitions.
- Data safety: minimal Personally Identifiable Information (PII) in AI prompts; encrypted storage.

## Ethical & Privacy Considerations

- Transparent data retention policies, automatic file deletion on inactivity.
- GDPR-aligned data management and user consent.
- Secure authentication and per-user data isolation.
