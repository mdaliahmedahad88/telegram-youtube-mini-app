<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>AhadTube</title>

<style>
body {
  margin: 0;
  background: #0f0f0f;
  color: white;
  font-family: Arial, sans-serif;
}

header {
  padding: 15px;
  background: #0f0f0f;
  position: sticky;
  top: 0;
  z-index: 10;
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
  padding: 12px;
  border-radius: 22px;
  border: 1px solid #444;
  background: #181818;
  color: white;
  font-size: 16px;
}

button {
  padding: 12px 16px;
  border: 0;
  border-radius: 22px;
  background: #22c55e;
  color: white;
  font-weight: bold;
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
  aspect-ratio: 16/9;
  object-fit: cover;
}

.info {
  padding: 8px 12px;
}

.title {
  font-size: 17px;
  font-weight: bold;
}

.channel {
  color: #aaa;
  margin-top: 6px;
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
placeholder="Search YouTube..."
onkeydown="if(event.key==='Enter') searchVideos()"
>

<button onclick="searchVideos()">Search</button>

</div>

</header>

<div id="status">
Ready to search
</div>

<main id="videos"></main>

<script>

const API = "https://pipedapi.moomoo.me";

async function searchVideos() {

const query =
document.getElementById("searchBox").value.trim();

if (!query) {
alert("Please enter a search.");
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
"Search server unavailable.";

console.log(error);

}

}

function showVideos(items) {

const container =
document.getElementById("videos");

if (!items.length) {

document.getElementById("status").innerText =
"No videos found.";

return;

}

document.getElementById("status").innerText =
items.length + " results";

items.forEach(video => {

const videoId =
video.url?.split("v=")[1] || "";

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
>

<div class="info">

<div class="title">
${escapeHtml(video.title || "")}
</div>

<div class="channel">
${escapeHtml(video.uploaderName || "")}
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

</script>

</body>
</html>
