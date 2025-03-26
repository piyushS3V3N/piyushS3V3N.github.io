---
layout: page
title: About
---

<style>
@keyframes typing {
  from { width: 0; }
  to { width: 100%; }
}

@keyframes blink {
  50% { border-color: transparent; }
}

.animated-text {
  font-family: 'Courier New', monospace;
  color: #0f0;
  background-color: black;
  padding: 10px;
  display: inline-block;
  white-space: nowrap;
  overflow: hidden;
  border-right: 2px solid #0f0;
  font-size: 1.2em;
  border-radius: 5px;
  margin-top: 10px;
  width: 100px;
  height: 100px;
  display: flex;
  align-items: center;
  justify-content: center;
}
</style>

# 👤 Piyush Parashar

**📍 Location:** Delhi, India  
**✉️ Email:** [piyushparashar2k@gmail.com](mailto:piyushparashar2k@gmail.com)  
**🐙 GitHub:** [github.com/piyushS3V3N](https://github.com/piyushS3V3N)

---

## 💼 Professional Summary

Results-driven software engineer specializing in Java, C/C++, Python, and DevOps. Experienced in building scalable, automated CI/CD pipelines and ensuring robust API security.

---

## 🔧 Technical Skills

- **Languages:** C, C++, Python, Java
- **DevOps & MLOps:** CI/CD, API Gateway, Docker, Kubernetes, Cloud Services
- **Machine Learning & NLP:** TensorFlow, NLTK, Flask
- **Tools:** OpenCV, MS Office, G Suite, Photoshop

---

## 💻 Experience

### 🖥️ Journey at Nagarro

<div id="api-auth" class="animated-text"></div>
- Developed Java Spring Boot applications with automated CI/CD workflows.
- Worked on API Gateway and Authorization Services using JVM & GraalVM.
- Deployed scalable applications with Kubernetes & Docker.

### 🧠 ML-Based Project Development

<div id="ml-model" class="animated-text"></div>
- Built an intelligent chatbot API using TensorFlow, NLTK, and Flask.
- Optimized model performance for high accuracy rates.

---

## 📂 Projects & Open Source

- [Feisharu Ninshki – Face Recognition System](https://github.com/piyushS3V3N/FeisharuNinshki)
- [MytoolScanner – Terminal Scanning Tool](https://github.com/piyushS3V3N/Mytoolscanner)
- [SHA-256 BruteForcer (C)](https://github.com/piyushS3V3N/Hashing)

---

## 🎓 Certifications

- CISCO: Introduction to Cyber Security
- Penetration Testing, Incident Response & Forensics
- CISCO: Cyber Security Essentials

---

## 🏆 Achievements

- Led scalable application development within tight deadlines.
- Recognized for creative problem-solving and prototype development.
- Conducted in-depth root cause analysis for system improvements.

<script>
function animateText(elementId, texts, interval) {
  let index = 0;
  const element = document.getElementById(elementId);
  function updateText() {
    element.textContent = texts[index];
    index = (index + 1) % texts.length;
    setTimeout(updateText, interval);
  }
  updateText();
}

animateText("api-auth", ["🔗 Sending Request...\n", "🔐 Authenticating...\n", "✅ Access Granted!", "🔄 Fetching Data...\n"], 1000);
animateText("ml-model", ["📂 Loading Dataset...\n", "📊 Training Model...\n", "🤖 Optimizing Weights...\n", "🎯 Model Ready!"], 1000);
</script>
