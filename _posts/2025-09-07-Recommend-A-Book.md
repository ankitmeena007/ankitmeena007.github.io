---
title: "Recommend a book"
date: 2025-09-07 00:00:00 +0800
categories: [Books]
tags: [Books, Book recommendations]
---

### Share your recommendations below.
They will be added to the recommendations cloud. Thanks!

<div id="wordcloud-section" style="text-align:center;">
  <div id="wordcloud" style="width:100%; height:400px; margin:1rem auto;"></div>
</div>

<h3 style="text-align:center;">Recommend a Book</h3>
<p style="text-align:center;">Fill out the form below to share your favorite book:</p>

<form id="book-form" class="modern-form">
  <label>Your Name (optional)</label>
  <input type="text" id="name" name="entry.1008954829" placeholder=""/>
  <label>Book Recommendation</label>
  <input type="text" id="book" name="entry.1518169936" placeholder="" required/>
  <button type="submit">Submit</button>
  <p id="thank-message" class="thank-message" style="text-align:center; display:none;"></p>
</form>

<iframe name="hidden_iframe" style="display:none;"></iframe>

<style>
.modern-form { display:flex; flex-direction:column; max-width:500px; margin:2rem auto; gap:1rem; }
.modern-form label { font-weight:600; margin-bottom:0.3rem; }
.modern-form input { padding:0.7rem 1rem; border-radius:8px; font-size:1rem; outline:none; transition: all 0.2s; }
.modern-form input:focus { box-shadow:0 0 5px rgba(79,70,229,0.3); }
.modern-form button { padding:0.8rem 1rem; font-size:1rem; border:none; border-radius:8px; font-weight:600; cursor:pointer; transition: all 0.15s ease-in-out; }
.modern-form button:hover { transform:scale(1.02); }
.modern-form button:active { transform:scale(0.97); box-shadow: inset 0 2px 4px rgba(0,0,0,0.2); }
.thank-message { margin-top:1rem; font-weight:600; animation:fadeIn 0.3s ease-in-out; }
@keyframes fadeIn { from { opacity:0; transform:translateY(-5px);} to {opacity:1; transform:translateY(0);} }
</style>
<script src="https://cdnjs.cloudflare.com/ajax/libs/wordcloud2.js/1.1.1/wordcloud2.min.js"></script>
<script>
document.addEventListener("DOMContentLoaded", () => {
  const form = document.getElementById("book-form");
  const thankMessage = document.getElementById("thank-message");
  const googleFormAction = "https://docs.google.com/forms/d/1AU2pD59UoAfBe8egyBJc5NGH_kScMf4hnvuZ4_5aInM/formResponse";
  form.addEventListener("submit", (e) => {
    e.preventDefault();
    const name = document.getElementById("name").value.trim() || "Anonymous";
    const book = document.getElementById("book").value.trim();
    if (!book) return;
    document.getElementById("name").value = name;
    document.getElementById("book").value = book;
    form.action = googleFormAction;
    form.target = "hidden_iframe";
    form.submit();
    showMessage("Thank you for your recommendation!");
    form.reset();
  });
  function showMessage(msg) {
    thankMessage.style.display = "block";
    thankMessage.style.color = "#10b981";
    thankMessage.textContent = msg;
    setTimeout(() => { thankMessage.style.display = "none"; }, 2000);
  }
  const sheetScriptURL = "https://script.google.com/macros/s/AKfycbzG2dC3YawEKvlNQ5lLta9shTywRy62vaq02LLH8dsocTKl0v-gPmSmqzAtD6kOZKA/exec";
  async function loadWordCloud() {
    try {
      const res = await fetch(sheetScriptURL);
      const data = await res.json();
      const counts = {};
      data.forEach(entry => {
        if(entry.book) {
          const b = entry.book.trim();
          counts[b] = (counts[b] || 0) + 1;
        }
      });
      const words = Object.entries(counts).map(([book, count]) => [book, count * 10]);
      WordCloud(document.getElementById("wordcloud"), {
        list: words,
        gridSize: 16,
        weightFactor: 2,
        color: () => getComputedStyle(document.body).color,
        backgroundColor: "transparent"
      });
    } catch(err) {
      console.error("Error loading word cloud:", err);
    }
  }
  loadWordCloud();
});
</script>
