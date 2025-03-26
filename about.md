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

.terminal-animation {
  font-family: 'Courier New', monospace;
  color: #0f0;
  background-color: black;
  padding: 10px;
  display: inline-block;
  white-space: nowrap;
  overflow: hidden;
  border-right: 2px solid #0f0;
  animation: typing 3s steps(30, end), blink 0.8s infinite;
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
  width: 40px;
  height: 40px;
  display: flex;
  align-items: center;
  justify-content: center;
}

h1, h2, h3 {
  color: #333;
  font-family: 'Arial', sans-serif;
}

.container {
  max-width: 800px;
  margin: auto;
  padding: 20px;
}
</style>

<div class="container">
  <h1><i class="fas fa-user-circle"></i> Piyush Parashar</h1>
  
  <p><strong><i class="fas fa-map-marker-alt"></i> Location:</strong> Delhi, India</p>
  <p><strong><i class="fas fa-envelope"></i> Email:</strong> <a href="mailto:piyushparashar2k@gmail.com">piyushparashar2k@gmail.com</a></p>
  <p><strong><i class="fab fa-github"></i> GitHub:</strong> <a href="https://github.com/piyushS3V3N" target="_blank">github.com/piyushS3V3N</a></p>

  <hr>
  
  <h2><i class="fas fa-briefcase"></i> Professional Summary</h2>
  <p>Results-driven software engineer specializing in Java, C/C++, Python, and DevOps. Experienced in building scalable, automated CI/CD pipelines and ensuring robust API security.</p>

  <hr>
  
  <h2><i class="fas fa-microchip"></i> Technical Skills</h2>
  <ul>
    <li><i class="fas fa-code"></i> <strong>Languages:</strong> C, C++, Python, Java</li>
    <li><i class="fas fa-server"></i> <strong>DevOps & MLOps:</strong> CI/CD, API Gateway, Docker, Kubernetes, Cloud Services</li>
    <li><i class="fas fa-robot"></i> <strong>Machine Learning & NLP:</strong> TensorFlow, NLTK, Flask</li>
    <li><i class="fas fa-tools"></i> <strong>Tools:</strong> OpenCV, MS Office, G Suite, Photoshop</li>
  </ul>

  <hr>

  <h2><i class="fas fa-laptop-code"></i> Experience</h2>

  <h3><i class="fas fa-hdd"></i> Journey at Nagarro</h3>
  <div id="api-auth" class="animated-text"></div>
  <ul>
    <li>Developed Java Spring Boot applications with automated CI/CD workflows.</li>
    <li>Worked on API Gateway and Authorization Services using JVM & GraalVM.</li>
    <li>Deployed scalable applications with Kubernetes & Docker.</li>
  </ul>

  <h3><i class="fas fa-brain"></i> ML-Based Project Development</h3>
  <div id="ml-model" class="animated-text"></div>
  <ul>
    <li>Built an intelligent chatbot API using TensorFlow, NLTK, and Flask.</li>
    <li>Optimized model performance for high accuracy rates.</li>
  </ul>

  <hr>

  <h2><i class="fas fa-project-diagram"></i> Projects & Open Source</h2>
  <ul>
    <li><a href="https://github.com/piyushS3V3N/FeisharuNinshki" target="_blank">Feisharu Ninshki – Face Recognition System</a></li>
    <li><a href="https://github.com/piyushS3V3N/Mytoolscanner" target="_blank">MytoolScanner – Terminal Scanning Tool</a></li>
    <li><a href="https://github.com/piyushS3V3N/Hashing" target="_blank">SHA-256 BruteForcer (C)</a></li>
  </ul>

  <hr>
  
  <h2><i class="fas fa-certificate"></i> Certifications</h2>
  <ul>
    <li>CISCO: Introduction to Cyber Security</li>
    <li>Penetration Testing, Incident Response & Forensics</li>
    <li>CISCO: Cyber Security Essentials</li>
  </ul>

  <hr>
  
  <h2><i class="fas fa-trophy"></i> Achievements</h2>
  <ul>
    <li>Led scalable application development within tight deadlines.</li>
    <li>Recognized for creative problem-solving and prototype development.</li>
    <li>Conducted in-depth root cause analysis for system improvements.</li>
  </ul>
</div>

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

animateText("api-auth", ["🔗 Sending Request...", "🔐 Authenticating...", "✅ Access Granted!", "🔄 Fetching Data..."], 1000);
animateText("ml-model", ["📂 Loading Dataset...", "📊 Training Model...", "🤖 Optimizing Weights...", "🎯 Model Ready!"], 1000);
</script>
