<!DOCTYPE html>
<html lang="bn">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Saki Store | Secure Admin</title>
    <link href="https://fonts.googleapis.com/css2?family=Poppins:wght@400;600&display=swap" rel="stylesheet">
    <style>
        body { font-family: 'Poppins', sans-serif; background: #f0f2f5; margin: 0; display: flex; justify-content: center; align-items: center; min-height: 100vh; }
        .card { background: white; padding: 30px; border-radius: 20px; box-shadow: 0 10px 30px rgba(0,0,0,0.1); width: 100%; max-width: 400px; border-top: 5px solid #00a884; }
        h2 { text-align: center; color: #333; margin-bottom: 25px; }
        input { width: 100%; padding: 12px; margin-bottom: 15px; border: 1.5px solid #ddd; border-radius: 10px; box-sizing: border-box; outline: none; transition: 0.3s; }
        input:focus { border-color: #00a884; }
        button { width: 100%; padding: 12px; background: #00a884; color: white; border: none; border-radius: 10px; font-weight: bold; cursor: pointer; transition: 0.3s; }
        button:hover { background: #008f6f; }
        #msg { text-align: center; font-size: 14px; margin-top: 15px; font-weight: 600; }
    </style>
</head>
<body>

<div class="card">
    <h2>🚀 Admin Dashboard</h2>
    <input type="password" id="pass" placeholder="পাসওয়ার্ড (1234)">
    <input type="text" id="name" placeholder="অ্যাপের নাম">
    <input type="text" id="icon" placeholder="আইকন লিঙ্ক (URL)">
    <input type="text" id="link" placeholder="ডাউনলোড লিঙ্ক (APK)">
    <input type="text" id="desc" placeholder="ছোট বর্ণনা">
    <button onclick="addApp()">Publish Now</button>
    <p id="msg"></p>
</div>

<script>
    // আপনার তথ্যগুলো এখানে সেট করা আছে
    const TOKEN = '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';
    const USER = 'saki1836';
    const REPO = 'saki1836.github.io';

    async function addApp() {
        const pass = document.getElementById('pass').value;
        const name = document.getElementById('name').value;
        const icon = document.getElementById('icon').value;
        const link = document.getElementById('link').value;
        const desc = document.getElementById('desc').value;
        const msg = document.getElementById('msg');

        if(pass !== '1234') { alert("ভুল পাসওয়ার্ড!"); return; }
        if(!name || !link) { alert("নাম এবং লিঙ্ক দিন!"); return; }

        msg.innerText = "⏳ সাইট আপডেট হচ্ছে...";
        msg.style.color = "orange";

        try {
            // ১. index.html এর ডেটা আনা
            const res = await fetch(`https://api.github.com/repos/${USER}/${REPO}/contents/index.html`);
            const data = await res.json();
            const content = decodeURIComponent(escape(atob(data.content)));
            const sha = data.sha;

            // ২. নতুন অ্যাপ কার্ড তৈরি
            const appCard = `
    <div class="app-card">
        <img src="${icon || 'https://via.placeholder.com/100'}" alt="${name}">
        <h3>${name}</h3>
        <p>${desc || 'প্রিমিয়াম অ্যাপ।'}</p>
        <a href="${link}" class="btn-dl">Download</a>
    </div>`;

            const newContent = content.replace('<div class="app-grid" id="appGrid">', `<div class="app-grid" id="appGrid">${appCard}`);

            // ৩. GitHub এ পাঠানো
            const update = await fetch(`https://api.github.com/repos/${USER}/${REPO}/contents/index.html`, {
                method: 'PUT',
                headers: { 'Authorization': `token ${TOKEN}`, 'Content-Type': 'application/json' },
                body: JSON.stringify({
                    message: `Added ${name}`,
                    content: btoa(unescape(encodeURIComponent(newContent))),
                    sha: sha
                })
            });

            if(update.ok) {
                msg.innerText = "✅ সফল হয়েছে! ২-৩ মিনিট পর সাইট দেখুন।";
                msg.style.color = "green";
            } else { throw new Error(); }
        } catch (e) {
            msg.innerText = "❌ ভুল হয়েছে! আবার চেষ্টা করুন।";
            msg.style.color = "red";
        }
    }
</script>

</body>
</html>
