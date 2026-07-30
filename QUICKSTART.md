# Firebase Multi-User Sync - Quick Start Guide

Get Expense Dinas running with Firebase real-time sync in 15 minutes.

---

## 🚀 Quick Setup (15 min)

### Step 1: Create Firebase Project (5 min)
1. Visit [Firebase Console](https://console.firebase.google.com)
2. Click **Create Project** → Enter name "expense-dinas"
3. Disable Google Analytics (optional) → **Create project**
4. Wait for project creation (2-3 min)

### Step 2: Enable Firebase Services (3 min)

**Firestore Database:**
- Go to **Build > Firestore Database**
- Click **Create Database**
- Region: **asia-southeast1** (or nearest to you)
- Security rules: **Start in production mode**
- Click **Create**

**Authentication:**
- Go to **Build > Authentication**
- Click **Get started**
- Enable: **Email/Password**, **Google** (optional)
- Click **Save**

**Cloud Storage:**
- Go to **Build > Storage**
- Click **Get started**
- Region: Same as Firestore (asia-southeast1)
- Security rules: **Start in production mode**
- Click **Done**

### Step 3: Get Firebase Config (2 min)
1. Go to **Project Settings** (gear icon)
2. Under "Your apps", click **Create new web app**
3. App name: "Expense Dinas Web"
4. Register app
5. **Copy the config** (API key, auth domain, etc.)

### Step 4: Local Development Setup (5 min)

**File: `.env.local`** (create this in `/home/user/Pub/`)
```env
VITE_FIREBASE_API_KEY=AIzaSy... (paste from step 3)
VITE_FIREBASE_AUTH_DOMAIN=expense-dinas-xxxxx.firebaseapp.com
VITE_FIREBASE_PROJECT_ID=expense-dinas-xxxxx
VITE_FIREBASE_STORAGE_BUCKET=expense-dinas-xxxxx.appspot.com
VITE_FIREBASE_MESSAGING_SENDER_ID=123456789
VITE_FIREBASE_APP_ID=1:123456789:web:abc123def456
VITE_ENVIRONMENT=development
```

⚠️ **Important**: `.env.local` is in `.gitignore` - never commit it!

---

## ✅ Testing Locally

### Test 1: Offline Mode (2 min)
1. Open `index.html` in browser
2. Should see "⚠️ Offline" in top-right
3. Create trip locally (works without Firebase)
4. Expenses can be added, edited, deleted

### Test 2: Authentication (3 min)
1. Click **"🔓 Sign In"** button (top-right)
2. **Create Account**: 
   - Email: `test@example.com`
   - Password: `Test123456!`
   - Click **Sign Up**
3. Check email inbox for verification link
4. Click link to verify
5. Return to app, **click "🔓 Sign In"** again
6. Enter credentials, click **Sign In**
7. Should now see "✅ Synced" and user email

### Test 3: Permission Checking (2 min)
1. Sign in (become Owner of trips)
2. Create a trip
3. Add an expense (should work - Owner can add)
4. Edit an expense (should work - Owner can edit)
5. Delete an expense (should work - Owner can delete)

**For Viewer role** (requires manual Firestore setup):
1. Add another user to sharedWith as "viewer"
2. Have that user sign in and try to add expense
3. Should see error: "❌ Anda tidak memiliki akses..."

---

## 🔧 Troubleshooting

### "Firebase config not configured"
**Solution**:
1. Check `.env.local` exists with correct values
2. Verify API key starts with `AIzaSy`
3. Verify project ID matches Firebase Console
4. Restart browser (refresh might not reload env)

### "Email verification required"
**Solution**:
1. Check your email (including spam folder)
2. If no email received:
   - Check firebase Console > Auth > Users
   - Delete test user
   - Create new account with different email
   - Wait 2-3 seconds for email

### "Permission denied" in browser console
**Solution**:
1. Deploy security rules: `firebase deploy --only firestore:rules`
2. Check Firebase Console > Security Rules
3. Verify rules match `firestore.rules` in repository

### "Sync shows Offline even when online"
**Solution**:
1. Check browser console (F12 > Console tab)
2. Look for error messages about Firebase
3. Verify internet connection
4. Check `.env.local` has correct project ID

---

## 📱 Test Multi-User Sync (Future)

Once real-time listeners are implemented:

1. **User A**: Sign in, create trip
2. **User B**: In different browser/device, sign in
3. **User A**: Add expense
4. **User B**: Should see it appear instantly (no refresh needed)

**Currently** (Phase 2): User B needs to refresh to see changes.

---

## 🚀 Production Deployment

When ready to deploy to production:

### 1. Deploy Firestore Rules
```bash
cd /home/user/Pub
firebase deploy --only firestore:rules
```

### 2. Deploy Cloud Storage Rules
```bash
firebase deploy --only storage
```

### 3. Deploy to Firebase Hosting
```bash
firebase deploy
```

Your app will be live at: `https://your-project.web.app`

### 4. Set GitHub Secrets
```bash
firebase login:ci
# Copy token
```

Then in GitHub > Settings > Secrets:
- `FIREBASE_TOKEN` = (token from above)
- `FIREBASE_PROJECT_ID` = your-project-id

Now every push to `main` auto-deploys!

---

## 📚 Key Files

| File | Purpose |
|------|---------|
| `index.html` | Main app with Firebase integration |
| `.env.example` | Template for Firebase config (commit this) |
| `.env.local` | Your actual Firebase credentials (don't commit!) |
| `firestore.rules` | Security rules (commit, deploy with `firebase deploy`) |
| `storage.rules` | Storage rules (commit, deploy with `firebase deploy`) |
| `firebase.json` | Deployment config (commit this) |
| `FIREBASE_SETUP.md` | Detailed setup guide (70+ steps) |
| `IMPLEMENTATION_STATUS.md` | Full feature status and roadmap |

---

## 🎯 Current Features

✅ **Working Now**:
- Offline mode (full access)
- Sign up / Sign in with email
- Permission checking (Owner/Editor/Viewer)
- Trip syncing to Firestore
- Offline indicator in UI

⏳ **Coming Soon**:
- Real-time expense updates (refresh not needed)
- Share trip UI (add collaborators in app)
- Photo Cloud Storage upload
- Presence indicators ("User X is editing...")

---

## ❓ Common Questions

**Q: Can I use the app without Firebase?**
A: Yes! Offline mode works fully. Sign in is optional.

**Q: What if my internet goes down?**
A: All changes save locally (IndexedDB). They sync when you reconnect.

**Q: Can I test with multiple users?**
A: Yes! Open in two browsers, sign in as different users. Currently, refresh to see changes (real-time coming soon).

**Q: How much does Firebase cost?**
A: Free tier includes 50K reads/day, 20K writes/day. Usually free for small teams.

**Q: Is my data secure?**
A: Yes! HTTPS encryption, Firestore security rules, email verification, role-based access.

---

## 🔗 Useful Links

- [Firebase Console](https://console.firebase.google.com) - Manage project
- [Firestore Rules Reference](https://firebase.google.com/docs/firestore/security/get-started)
- [Firebase CLI Guide](https://firebase.google.com/docs/cli)
- [GitHub Actions Setup](https://github.com/FirebaseExtended/action-hosting-deploy)

---

## 📞 Need Help?

See `FIREBASE_SETUP.md` for 70+ detailed troubleshooting steps.

---

**Last Updated**: 2026-07-30
**Status**: Ready for local testing
