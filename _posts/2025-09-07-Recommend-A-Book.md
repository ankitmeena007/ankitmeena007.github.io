---
title: "📚 Recommend a book"
date: 2025-09-07 00:00:00 +0800
categories: [Books]
tags: [Books, Book recommendations]
description: Share your recommendations below. Thanks!
---

### Share your recommendations below.
They will be added to the recommendations word cloud.
Thanks!

<div id="wordcloud-section" style="text-align:center;">
  <div id="wordcloud" style="width:100%; height:400px; margin:1rem auto;"></div>
</div>

<h3 style="text-align:center;">Recommend a Book</h3>
<p style="text-align:center;">Fill out the form below to share your favorite book:</p>

<form id="book-form" class="modern-form">
  <label>Your Name (optional)</label>
  <input type="text" id="name" name="name" placeholder=""/>
  <label>Book Recommendation</label>
  <input type="text" id="book" name="book" placeholder="" required/>
  <button type="submit">Submit</button>
  <p id="thank-message" class="thank-message" style="text-align:center; display:none;"></p>
</form>

<style>
.modern-form {
  display:flex; flex-direction:column; max-width:500px; margin:2rem auto; gap:1rem;
}
.modern-form label { font-weight:600; margin-bottom:0.3rem; }
.modern-form input {
  padding:0.7rem 1rem; border-radius:8px; font-size:1rem;
  outline:none; transition: all 0.2s;
}
.modern-form input:focus { box-shadow:0 0 5px rgba(79,70,229,0.3); }
.modern-form button {
  padding:0.8rem 1rem; font-size:1rem; border:none; border-radius:8px;
  font-weight:600; cursor:pointer; transition: all 0.15s ease-in-out;
}
.modern-form button:hover { transform:scale(1.02); }
.modern-form button:active {
  transform:scale(0.97); box-shadow: inset 0 2px 4px rgba(0,0,0,0.2);
}
.thank-message { margin-top:1rem; font-weight:600; animation:fadeIn 0.3s ease-in-out; }
@keyframes fadeIn { from { opacity:0; transform:translateY(-5px);} to {opacity:1; transform:translateY(0);} }
</style>

<script src="https://cdnjs.cloudflare.com/ajax/libs/wordcloud2.js/1.1.1/wordcloud2.min.js"></script>
<script>
const scriptURL = "https://script.google.com/macros/s/AKfycbxvXkCTLnIqi1P2yS7WzySwFQH_yTauM1NJ3w3YyGNey_3-a_H3fLP542mKkxAwEwM/exec"; // <-- paste your deployed Apps Script Web App URL
const form = document.getElementById("book-form");
const thankMessage = document.getElementById("thank-message");
// Form submission
form.addEventListener("submit", async e => {
  e.preventDefault();
  const name = document.getElementById("name").value || "Anonymous";
  let book = document.getElementById("book").value;
  // Normalize book title
  book = book.trim().replace(/\s+/g,' ')
    .split(' ').map(w => w.charAt(0).toUpperCase() + w.slice(1).toLowerCase())
    .join(' ');
  try {
    const payload = { name, book };
    const res = await fetch(scriptURL, {
      method:"POST",
      headers: { "Content-Type":"application/json" },
      body: JSON.stringify(payload)
    });
    const result = await res.json();
    if(result.result === "success") {
      thankMessage.style.display = "block";
      thankMessage.style.color = "#10b981";
      thankMessage.textContent = "Thanks for your recommendation!";
      form.reset();
      loadWordCloud(); // refresh
    } else {
      thankMessage.style.display = "block";
      thankMessage.style.color = "#ef4444";
      thankMessage.textContent = "Error: " + result.message;
    }
  } catch(err) {
    console.error(err);
    thankMessage.style.display = "block";
    thankMessage.style.color = "#ef4444";
    thankMessage.textContent = "Error submitting. Please try again.";
  }
  setTimeout(() => { thankMessage.style.display = "none"; }, 2000);
});
// Load word cloud including ALL books
async function loadWordCloud() {
  try {
    const res = await fetch(scriptURL);
    const data = await res.json();
    const counts = {};
    data.forEach(entry => {
      if(entry.book) {
        const b = entry.book.trim().replace(/\s+/g,' ')
          .split(' ').map(w => w.charAt(0).toUpperCase() + w.slice(1).toLowerCase())
          .join(' ');
        if(b) counts[b] = (counts[b] || 0) + 1;
      }
    });
    const words = Object.entries(counts).map(([book, count]) => [book, count * 10]);
    WordCloud(document.getElementById("wordcloud"), {
      list: words, gridSize:16, weightFactor:2,
      color:() => getComputedStyle(document.body).color,
      backgroundColor:"transparent"
    });
  } catch(err) {
    console.error("Error loading word cloud:", err);
  }
}
loadWordCloud();
</script>
