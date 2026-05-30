# Google Cloud Associate Cloud Engineer (ACE) Study Guide

<figure>
  <img src="https://miro.medium.com/v2/resize:fit:640/format:webp/0*XdPsBsUseYD08_Ne" alt="Google Cloud Platform - Associate Cloud Engineer" width=200>
  <figcaption><center>CGoogle Cloud Platform - Associate Cloud Engineer<br><i><a href="https://wings.medium.com/how-to-ace-the-google-cloud-platform-associate-cloud-engineer-exam-5a39e12f091f">Image source: Medium</a></i></center></figcaption>
</figure>

> **Note: These are my personal study notes that I am using to prepare myself for the _Google Cloud Associate Cloud Engineer (ACE)_ certification exam.**
>
> And I prompted them all out.

I [passed](https://www.credly.com/badges/bdb2e192-6920-4344-8feb-bace577d3460).

## Read the Book

Notes in this repository are compiled into a highly readable, searchable online book using **mdBook**.

**[Read the live study guide here](https://cookieMr.github.io/gcp_ace_notes/)**

### e-book

If you preffer the offline mode you can download [epub](https://github.com/cookieMr/gcp_ace_notes/releases/download/latest/GCP.ACE.Study.Guide.2026.epub) file.

### Flash cards

You can download [flash cards](https://github.com/cookieMr/gcp_ace_notes/gpc_ace_anki_cards.txt) (for [Anki application](https://apps.ankiweb.net/)) for study for thie GCP ACE.

## Project Structure

The study material is organized into specific Google Cloud services and concepts. All source material is written in Markdown and located in the `src/` directory. Key areas covered include:

- **Compute Services:** Compute Engine, GKE, Cloud Run, App Engine, Cloud Functions.
- **Storage & Databases:** Cloud Storage, Cloud SQL, Cloud Spanner, BigQuery, Firestore, Bigtable, Memorystore.
- **Networking:** VPC Networks, Load Balancers, Cloud DNS, Hybrid Connectivity (Cloud VPN, Cloud Interconnect).
- **Operations & Security:** IAM, Cloud Logging, Cloud Monitoring, VPC Service Controls, Cloud Armor, Secret Manager.
- **Migration Tools:** Migrate to Virtual Machines, Database Migration Service, Storage Transfer Service.

## Building Locally

If you want to run this book locally to study offline or modify the notes, you will need the [mdBook](https://rust-lang.github.io/mdBook/) command-line tool.

1. **Install mdBook** (requires the Rust toolchain):
   ```bash
   cargo install mdbook mdbook-epub
   ```
2. **Serve the book locally**:
   ```bash
   mdbook serve --open
   ```
   This compiles the Markdown files and opens a local web server at [http://localhost:3000](http://localhost:3000) with hot-reloading enabled.

## Content Generation

The core content and technical facts within these Markdown files were initially structured and generated with the assistance of AI, then curated, reviewed, and formatted specifically for this mdBook layout.

## Mock Tests (Free)

IMHO these are a poor aproximation of the real exam. So I suggest to look for a paid options (I used Udemy).

- [OpenExamPrep](https://open-exam-prep.com/practice/gcp-ace)
  - 200 short questions
  - 3/5 ⭐
- [Exam Cert App](https://www.examcert.app/exams/gcp-ace/)
  - 3 free exam sets
  - only on mobile, can review progress, no copy-text so i had to rewrite quesiton to GTP Friend for verification
  - 3/5 ⭐
- [Certification Practice](https://certificationpractice.com/practice-exams/google-associate-cloud-engineer)
  - Full-length exam-style questions, realistic format
  - cannot review questions after answering all 60 quesitons
  - 2/5 ⭐
- [Examzify](https://certifiedassociatecloudengineerstudyguide.examzify.com/)
  - 20 free questions looped in a set of 400 questions
  - 2/5 ⭐
- [ExamTopics](https://www.examtopics.com/exams/google/associate-cloud-engineer/view)
  - 20 free questions, answers need to be verified as they show only user's votes
  - 2/5 ⭐ (i would give way more stars if there were more questions)
- [Trusted Institute](https://trustedinstitute.com/practice/gcp-associate-cloud-engineer)
  - limited access, maybe 10-20 total questions for free
  - long questions with lots of text, good exam approximation
  - 1/5 ⭐
- [IT Exams](https://www.itexams.com/info/Associate-Cloud-Engineer)
  - 10 free exam questions already seen elsewere
  - 1/5 ⭐

## License

<figure>
  <img src="https://fsfe.org/graphics/gplv3-logo-red.png" alt="GNU GPL v3 Logo" width=200>
  <figcaption><center>GNU General Public License v3.0<br><i><a href="https://fsfe.org/activities/gplv3/diff-draft2-draft3.en.html">Image source: FSFE</a></i></center></figcaption>
</figure>

This project is licensed under the [GNU General Public License v3.0 (GPLv3)](https://www.gnu.org/licenses/gpl-3.0.en.html#license-text).

You are free to use, modify, and distribute this study guide, provided that any modifications or derivative works are also distributed under the same open-source GPLv3 license. See the [LICENSE](./LICENSE) file for more details.
