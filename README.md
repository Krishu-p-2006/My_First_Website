# KP Portfolio — setup guide

## 1. Firebase project banao (5 minute, free)
1. https://console.firebase.google.com pe jao → "Add project"
2. Project ke andar: gear icon → Project settings → "Your apps" → Web (</>) icon se app register karo
3. Jo `firebaseConfig` object milega, usse `js/firebase-config.js` me paste kar do (placeholder values replace karo)
4. Left sidebar → Build → **Firestore Database** → Create database → test mode se start karo
5. Left sidebar → Build → **Authentication** → Get started → Email/Password method enable karo
6. Authentication → Users tab → apna ek email/password add karo — yahi admin.html ka login hoga

## 2. Locally test karo
Browser seedha file:// se khole to Firebase module imports fail ho sakte hain (CORS). Isliye local server chalao:
```
cd kp-portfolio
python3 -m http.server 8000
```
Fir browser me `http://localhost:8000` kholo.

## 3. Deploy karo (Firebase Hosting — free)
```
npm install -g firebase-tools
firebase login
cd kp-portfolio
firebase init hosting    # public directory: . (current folder), single-page app: No
firebase deploy
```
Deploy hone ke baad ek free `.web.app` URL milega jo resume/LinkedIn pe daal sakte ho.

## 4. Daily use
- `admin.html` pe login karo
- Naya post likho → Publish dabao
- Wo turant homepage ke "Latest notes", blog page, aur streak grid me reflect ho jayega

## Firestore structure
- `posts` collection — har post ek document (ID = slug)
  - fields: `title`, `category`, `excerpt`, `content`, `tags[]`, `createdAt`
- `messages` collection — contact form se aaye messages (Firestore console se padh sakte ho)

## Security rule (important — Firestore me set karo)
Firestore Database → Rules tab me ye paste karo, taaki sirf tum (logged-in admin) post likh/edit/delete kar sako, baaki sab sirf padh sakein:
```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /posts/{postId} {
      allow read: if true;
      allow write: if request.auth != null;
    }
    match /messages/{msgId} {
      allow create: if true;
      allow read, update, delete: if request.auth != null;
    }
  }
}
```
