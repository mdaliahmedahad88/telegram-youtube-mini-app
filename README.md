<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>AhadTube</title>

<style>
* {
  box-sizing: border-box;
}

body {
  margin: 0;
  background: #0f0f0f;
  color: white;
  font-family: Arial, sans-serif;
}

header {
  position: sticky;
  top: 0;
  z-index: 10;
  background: #0f0f0f;
  padding: 14px;
}

.logo {
  font-size: 24px;
  font-weight: bold;
  margin-bottom: 12px;
}

.search {
  display: flex;
  gap: 8px;
}

input {
  flex: 1;
  padding: 12px 16px;
  border-radius: 24px;
  border: 1px solid #444;
  background: #181818;
  color: white;
  font-size: 16px;
}

button {
  border: 0;
  border-radius: 22px;
  padding: 10px 16px;
  background: #22c55e;
  color: white;
  font-weight: bold;
}

.categories {
  display: flex;
  gap: 8px;
  overflow-x: auto;
  padding: 10px 14px;
}

.categories button {
  background: #272727;
  white-space: nowrap;
}

#status {
  padding: 15px;
  color: #aaa;
}

.video {
  margin-bottom: 25px;
}

.thumbnail {
  width: 100%;
  aspect-ratio: 16 / 9;
  object-fit: cover;
  background: #222;
}

.info {
  padding: 8px 12px;
}

.title {
  font-size: 17px;
  font-weight: bold;
  line-height: 1.3;
}

.channel {
  margin-top: 6px;
  color: #aaa;
  font-size: 14px;
}

.views {
  color: #777;
  font-size: 13px;
  margin-top: 4px;
}

a {
  color: white;
  text-decoration: none;
}
</style>
</head>

<body>

<header>
  <div class="logo">▶ AhadTube</div>

  <div class="search">
    <input
      id="searchBox"
      type="text"
      placeholder="Search videos..."
      onkeydown="if(event.key==='Enter') searchVideos()"
    >

    <button onclick="searchVideos()">Search</button>
  </div>
</header>

<div class="categories">
  <button onclick="loadTrending()">Trending</button>
  <button onclick="searchTerm('music')">Music</button>
  <button onclick="searchTerm('gaming')">Gaming</button>
  <button onclick="searchTerm('news')">News</button>
  <button onclick="searchTerm('technology')">Technology</button>
</div>

<div id="status">Loading...</div>

<main id="videos"></main>

<script>

const API ="https://pipedapi.moomoo.me";;

async function searchVideos() {

  const query =
    document.getElementById("searchBox").value.trim();

  if (!query) {
    alert("Please enter something to search.");
    return;
  }

  document.getElementById("status").innerText =
    "Searching...";

  document.getElementById("videos").innerHTML = "";

  try {

    const response = await fetch(
      API + "/search?q=" +
      encodeURIComponent(query) +
      "&filter=videos"
    );

    if (!response.ok) {
      throw new Error("API error");
    }

    const data = await response.json();

    showVideos(data.items || []);

  } catch (error) {

    document.getElementById("status").innerText =
      "Search failed. The API server may be unavailable.";

    console.error(error);
  }
}

function searchTerm(term) {

  document.getElementById("searchBox").value = term;

  searchVideos();
}

async function loadTrending() {

  document.getElementById("status").innerText =
    "Loading trending videos...";

  document.getElementById("videos").innerHTML = "";

  try {

    const response = await fetch(
      API + "/trending?region=BD"
    );

    if (!response.ok) {
      throw new Error("API error");
    }

    const data = await response.json();

    showVideos(data);

  } catch (error) {

    document.getElementById("status").innerText =
      "Trending videos could not be loaded.";

    console.error(error);
  }
}

function showVideos(items) {

  const container =
    document.getElementById("videos");

  container.innerHTML = "";

  if (!items.length) {

    document.getElementById("status").innerText =
      "No videos found.";

    return;
  }

  document.getElementById("status").innerText =
    items.length + " videos found";

  items.forEach(video => {

    const videoId =
      video.url
        ? video.url.split("v=")[1]
        : "";

    const div =
      document.createElement("div");

    div.className = "video";

    div.innerHTML = `

      <a
        href="https://www.youtube.com/watch?v=${videoId}"
        target="_blank"
      >

        <img
          class="thumbnail"
          src="${video.thumbnail || ""}"
          loading="lazy"
        >

        <div class="info">

          <div class="title">
            ${escapeHtml(video.title || "Untitled")}
          </div>

          <div class="channel">
            ${escapeHtml(video.uploader || "")}
          </div>

          <div class="views">
            ${video.views || 0} views
          </div>

        </div>

      </a>
    `;

    container.appendChild(div);
  });
}

function escapeHtml(text) {

  const div =
    document.createElement("div");

  div.textContent = text;

  return div.innerHTML;
}

loadTrending();

</script>

</body>
</html>
