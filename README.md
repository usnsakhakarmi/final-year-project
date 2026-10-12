# 🎭 Camouflage: Video & Image Steganography

> **BCA Final Year Project**

Camouflage is a web application that **hides secret text messages inside videos and images**.
The video or image looks completely normal to anyone who sees it. Only a person who knows how (and, for videos, has the **secret key**) can pull the hidden message back out.

Hiding information inside another file like this is called **steganography**.

---

## 🤔 What is the project about?

Encryption turns a message into unreadable text, so people can see that something secret is being sent.
**Steganography goes one step further: it hides the fact that a message exists at all.**

Example: two friends share a normal-looking video. Nobody else knows it carries a secret message. The receiver uploads the video to Camouflage, enters the secret key, and reads the message.

**Where it can be useful:**
- 🔒 **Private communication:** send messages that look like normal media sharing
- ©️ **Copyright / watermarking:** hide an invisible ownership note inside your own video
- 📁 **Confidential data transfer:** move small sensitive text disguised as a media file

---

## ✨ What does it do?

| Feature | What happens |
|---|---|
| 🎬 **Encode Video** | Upload an `.avi` video, then type a secret key and a message (up to 50 characters). You get back a new video with the message hidden inside |
| 🎬 **Decode Video** | Upload the encoded video and enter the same secret key. The hidden message is shown on screen |
| 🖼️ **Encode Image** | Upload a `.png` / `.jpg` / `.jpeg` image and type a message (up to 80 characters). You get back an image with the message hidden inside |
| 🖼️ **Decode Image** | Upload the encoded image. The hidden message is shown on screen |
| 📥 **Download & Export** | Download the encoded video or image, and export the decoded message as a `.txt` file |
| ⏱️ **Stats** | Shows how long encoding took and compares the original and encoded file sizes |

---

## 🧠 How does it work? (Simple version)

### 1. LSB (Least Significant Bit) technique
Every pixel is made of colour numbers (Red, Green, Blue) from 0 to 255.
The app changes only the **last bit** of these numbers, so a colour changes by at most **±1**.
For example, `142` becomes `143`. The human eye cannot see that difference, but the computer can read those tiny changes back as **0s and 1s** and rebuild the message.

```
Original pixel:  (142, 203, 87)
Hidden pixel:    (143, 202, 87)   ← looks exactly the same
```

### 2. RC4 encryption (for videos)
Before the message is hidden in a video, it is first **encrypted with the RC4 cipher** using your secret key.
So even if someone finds the hidden bits, they can't read them without the key. That gives **two layers of security**: hidden *and* encrypted.

### 3. Smart placement in video
- The message is hidden starting at a **random frame** of the video.
- The **first frame** stores a small "table of contents": the message length and the frame where it starts.
- The video is saved in a **lossless format (AVI / FFV1)**. Formats like MP4 compress the pixels, which would destroy the hidden bits.

```
Video ENCODE:  Message ──RC4 + key──► encrypted bits ──LSB──► hidden in frames ──► encoded .avi
Video DECODE:  encoded .avi ──read LSBs──► encrypted bits ──RC4 + key──► original message
```

---

## 🛠️ Tech stack

| Part | Technology |
|---|---|
| Language | Python 3.10 |
| Web framework | Django 4.2 |
| Video processing | OpenCV, NumPy, MoviePy (FFmpeg) |
| Image processing | Pillow |
| Encryption | RC4 (written from scratch in `core/RC4/rc4.py`) |
| Frontend | HTML, CSS, JavaScript, Bootstrap 5 |
| Database | SQLite |
| Deployment-ready | Gunicorn, WhiteNoise, Procfile (Heroku) |

---

## 📂 Project structure (short)

```
camouflage/     → Django settings and main URL routes
core/           → Video steganography (encode/decode, RC4 encryption, main pages)
imageSteg/      → Image steganography (encode/decode)
static/         → CSS, JavaScript, images
media/          → Uploaded and encoded files
```

---

## 🚀 How to run it

```bash
# 1. Clone the project
git clone https://github.com/usnsakhakarmi/final-year-project.git
cd final-year-project

# 2. (Optional) create a virtual environment
python -m venv venv
venv\Scripts\activate          # Windows
# source venv/bin/activate     # macOS / Linux

# 3. Install packages
pip install -r requirements.txt

# 4. Create a .env file with a secret key
echo SECRET_KEY=any-long-random-text > .env

# 5. Create the media folders
mkdir media\encoded\image_encode media\images      # Windows
# mkdir -p media/encoded/image_encode media/images  # macOS / Linux

# 6. Set up the database and start the server
python manage.py migrate
python manage.py runserver
```

Then open **http://127.0.0.1:8000/** in your browser.

> ⚠️ **Note:** the image encoding code (`imageSteg/views.py`) saves to a hard-coded Windows path.
> Change it to a relative path such as `media/encoded/image_encode/encoded.png` before running the project on another computer.

📖 For the full technical explanation, see **[DOCUMENTATION.md](DOCUMENTATION.md)** and **[GUIDE.md](GUIDE.md)**.

---

## 🎓 My Final Year Experience (BCA)

<!-- ✏️ Edit the text in [brackets] with your own details. Feel free to reword anything so it sounds like you. -->

This project was my **final year project for the Bachelor of Computer Applications (BCA)** at **[College / University name]**, completed in **[Year]** under the guidance of **[Supervisor's name]**, together with **[team member names, if any]**.

**Why I chose this topic**
I wanted a project that combined **security** and **web development**. Most people know about encryption, but the idea of *hiding a message so nobody even knows it exists* felt interesting and different, so steganography became our topic.

**What I learned**
- **Django and web development:** how URLs, views, forms, templates and sessions work together in a real application.
- **How images and videos actually work:** that a video is just a list of frames, a frame is a grid of pixels, and a pixel is a set of numbers. Once I understood that, hiding data inside them made sense.
- **Bit-level thinking:** working with binary, LSBs and XOR, which I had only seen in theory classes before.
- **Cryptography basics:** implementing the RC4 cipher myself instead of just using a library.
- **NumPy and OpenCV:** handling large amounts of pixel data efficiently.
- **Teamwork and Git:** sharing code, dividing work, and fixing merge problems.

**Challenges I faced**
- 🎞️ **Hidden data disappearing:** my first attempts with MP4 videos lost the message because MP4 compression changes pixel values. Learning about **lossless formats (AVI / FFV1)** fixed this.
- 🧮 **Memory and speed:** videos have thousands of frames, so encoding was slow and used a lot of RAM. I had to free memory and clean up temporary files carefully.
- 🔑 **Wrong key handling:** decoding with a wrong key produced random symbols, so I added a check that shows a clear error message instead.
- 🎨 **Making it user-friendly:** a loading spinner, result pop-ups, download/export buttons, and a mobile-responsive layout.

**What this project gave me**
Before this project, most of my programming was small classroom exercises. Building Camouflage taught me how to **take an idea from research to a working product**: reading about an algorithm, turning it into code, connecting it to a web interface, testing it, and presenting it to examiners.
It gave me confidence in Python and web development, and it showed me how theory from subjects like **[e.g. Computer Networks, Data Security, Data Structures]** is used in real software.

> *"[Add a short personal line here, e.g. what you'd improve next or what you are most proud of.]"*

---

## 🔮 Future improvements

- Support more video formats (e.g. lossless MP4 / MKV)
- Add encryption with a key for images too
- Allow longer messages and file hiding (not just text)
- Use stronger modern encryption (e.g. AES) instead of RC4
- Add user accounts and history of encoded files
- Fix the hard-coded file path and make deployment easier

---

## 👤 Author

**[Your Name]** – BCA, [College / University], [Year]
GitHub: [@usnsakhakarmi](https://github.com/usnsakhakarmi)
