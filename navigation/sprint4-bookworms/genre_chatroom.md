---
layout: post
title: GENRE CHATROOM
hide: true
---


<title>Genre Chatroom</title>
<style>
 .navbar {
     width: 100%;
     max-width: 1200px;
     display: flex;
     justify-content: space-between;
     margin-bottom: 20px;
     background-color: #EDE4D9;
     padding: 10px 20px;
     color: white;
 }
 .navbar h1 {
     margin: 0;
 }
 .navbar button {
     padding: 10px;
     font-size: 16px;
     background-color: #C3A382;
     border: none;
     color: white;
     cursor: pointer;
 }
 .navbar button:hover {
     background-color: #444;
 }
 .dashboard {
     display: flex;
     flex-direction: column;
     width: 100%;
     max-width: 1200px;
     gap: 20px;
 }
 .section {
     width: 100%;
     padding: 20px;
     background-color: white;
     box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
     border-radius: none;
 }
 .post-container {
     max-height: 400px;
     overflow-y: auto;
     padding: 10px;
     background-color: #fff;
     border: none;
     border-radius: 8px;
     margin-bottom: 20px;
 }
 .post {
     border: 1px solid #ddd;
     padding: 10px;
     margin: 10px 0;
     border-radius: 8px;
     font-size: 1.1em;
     color: #4B4A40;
     position: relative;
 }
 .post-content {
     overflow: hidden;
     white-space: nowrap;
     text-overflow: ellipsis;
 }
 .see-more {
     color: blue;
     cursor: pointer;
     font-size: 0.9em;
     margin-left: 5px;
 }
 .reply-section {
     margin-top: 10px;
     padding-left: 20px;
     font-size: 0.9em;
     display: flex;
     align-items: center;
 }
 .reply-input {
     width: 80%;
     padding: 8px;
     font-size: 1em;
     border-radius: 8px;
     border: 1px solid #ddd;
 }
 .reply-btn {
     margin-left: 10px;
     padding: 5px 10px;
     font-size: 0.8em;
     cursor: pointer;
     border: none;
     background-color: #C3A382;
     color: white;
     border-radius: 8px;
 }
 textarea {
     width: 100%;
     padding: 10px;
     font-size: 1.1em;
     height: 120px;
     resize: none;
     margin-top: 10px;
     border-radius: 8px;
     border: 1px solid #ddd;
 }
 .send-btn {
     background-color: #C3A382;
     color: white;
     border: none;
     padding: 10px;
     width: 100%;
     font-size: 1.1em;
     cursor: pointer;
     border-radius: 8px;
     margin-top: 10px;
 }
</style>


<div class="navbar">
  <button onclick="location.href='{{site.baseurl}}/'">Home</button>
  <div>
    <input type="text" id="usernameInput" placeholder="Enter your username" style="padding: 5px; font-size: 16px; border-radius: 5px;">
    <button onclick="setUsername()" style="padding: 5px; font-size: 16px; background-color: #C3A382; border: none; color: white; cursor: pointer;">Set Username</button>
  </div>
</div>
<div class="dashboard">
  <div class="section post-container" id="postsSection">
    <!-- Posts will appear here -->
  </div>
  <div class="section">
    <textarea id="postInput" placeholder="Share your interests..."></textarea>
  </div>
  <div>
    <button class="send-btn" onclick="addPost()">Send</button>
  </div>
</div>

<script>
let loggedInUser = localStorage.getItem("loggedInUser") || "User123";
let posts = JSON.parse(localStorage.getItem("savedPosts")) || [];

function setUsername() {
    const usernameInput = document.getElementById("usernameInput").value;
    if (usernameInput) {
        loggedInUser = usernameInput;
        localStorage.setItem("loggedInUser", loggedInUser);
        alert(`Username set to: ${loggedInUser}`);
    } else {
        alert("Username cannot be empty!");
    }
}

function goHome() {
    window.location.href = "{{site.baseurl}}/";
}

function addPost() {
    const postInput = document.getElementById("postInput").value;
    if (postInput) {
        const newPost = { content: postInput, likes: 0, replies: [], user: loggedInUser };
        posts.push(newPost);
        localStorage.setItem("savedPosts", JSON.stringify(posts));
        document.getElementById("postInput").value = '';
        displayPosts();
    } else {
        alert("Post cannot be empty!");
    }
}

function displayPosts() {
    const postsSection = document.getElementById("postsSection");
    postsSection.innerHTML = '';
    posts.forEach((post, index) => {
        const postElement = document.createElement("div");
        postElement.className = "post";
        postElement.innerHTML = `
            <div class="post-content">${post.user} : ${post.content.substring(0, 100)}${post.content.length > 100 ? '...' : ''}</div>
            ${post.content.length > 100 ? '<span class="see-more" onclick="seeMore(' + index + ')">See more</span>' : ''}
            <button class="like-btn" onclick="likePost(${index})"> ❤️ ${post.likes}</button>
            <button class="delete-btn" onclick="deletePost(${index})">🗑️</button>
            <div class="reply-section">
                <input type="text" class="reply-input" placeholder="Reply..." id="replyInput${index}" onfocus="showReplySection(${index})">
                <button class="reply-btn" onclick="addReply(${index})">Reply</button>
            </div>
            <div class="replies" id="replies${index}">
                ${post.replies.map(reply => `<div class="reply">${post.user} : ${reply}</div>`).join('')}
            </div>
        `;
        postsSection.appendChild(postElement);
    });
    autoScroll();
}

function showReplySection(index) {
    document.getElementById(`replies${index}`).style.display = 'block';
}

function seeMore(index) {
    const postContent = posts[index].content;
    alert(postContent);
}

function likePost(index) {
    posts[index].likes++;
    localStorage.setItem("savedPosts", JSON.stringify(posts));
    displayPosts();
}

function deletePost(index) {
    posts.splice(index, 1);
    localStorage.setItem("savedPosts", JSON.stringify(posts));
    displayPosts();
}

function addReply(index) {
    const replyInput = document.getElementById(`replyInput${index}`);
    const replyText = replyInput.value;
    if (replyText) {
        posts[index].replies.push(replyText);
        localStorage.setItem("savedPosts", JSON.stringify(posts));
        replyInput.value = '';
        displayPosts();
    } else {
        alert("Reply cannot be empty!");
    }
}

function autoScroll() {
    const postsSection = document.getElementById("postsSection");
    postsSection.scrollTop = postsSection.scrollHeight;
}

displayPosts();
</script>
