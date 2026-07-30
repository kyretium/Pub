# Firebase Setup Guide for Expense Dinas

This guide walks you through setting up Firebase for the Expense Dinas multi-user expense tracking application with security best practices.

## Prerequisites

- GitHub account with admin access to kyretium/pub repository
- Google account to create Firebase project
- Basic understanding of environment variables

## Phase 1: Create Firebase Project

### Step 1: Create Firebase Project

1. Go to [Firebase Console](https://console.firebase.google.com)
2. Click **"Create a project"**
3. Enter project name: `expense-dinas` (or your preferred name)
4. Accept the terms and click **"Create project"**
5. Wait for project creation to complete (2-3 minutes)

### Step 2: Enable Required Firebase Services

Once your project is created:

#### Firestore Database
1. In Firebase Console, go to **Build > Firestore Database**
2. Click **"Create database"**
3. Select **Region**: Choose closest to your users (e.g., `asia-southeast1` for Indonesia)
4. Choose **Security rules**: Select **"Start in production mode"**
5. Click **"Create"**

#### Firebase Authentication
1. Go to **Build > Authentication**
2. Click **"Get started"**
3. In the **Sign-in method** tab, enable:
   - ✅ **Email/Password**
   - ✅ **Google** (optional, for easier sign-in)
4. For Email/Password:
   - Enable both "Email/Password" and "Email link (passwordless sign-in)"
5. Click **"Save"**

#### Cloud Storage
1. Go to **Build > Storage**
2. Click **"Get started"**
3. Select region: Same as Firestore (e.g., `asia-southeast1`)
4. Choose **Security rules**: Select **"Start in production mode"**
5. Click **"Done"**

#### Optional: Enable reCAPTCHA v3
1. Go to **Build > Authentication > Security > reCAPTCHA Enterprise**
2. Set up reCAPTCHA v3 for sign-up/sign-in protection
   - This prevents automated bot accounts

### Step 3: Create Web App Configuration

1. Go to **Project Settings** (gear icon top-left)
2. Click **"Your apps"** section
3. Click **"Add app"** > **Web**
4. Enter app name: `Expense Dinas Web`
5. Click **"Register app"**
6. Copy the Firebase config (you'll use this next)

Example config structure:
```javascript
{
  "apiKey": "AIzaSy...",
  "authDomain": "your-project.firebaseapp.com",
  "projectId": "your-project-id",
  "storageBucket": "your-project.appspot.com",
  "messagingSenderId": "123456789",
  "appId": "1:123456789:web:abc123def456"
}
```

---

## Phase 2: Configure Local Development Environment

### Step 1: Set Up Environment Variables

1. Copy the example environment file:
```bash
cp .env.example .env.local
```

2. Edit `.env.local` and fill in your Firebase credentials:
```env
VITE_FIREBASE_API_KEY=AIzaSy... (paste from Firebase config)
VITE_FIREBASE_AUTH_DOMAIN=your-project.firebaseapp.com
VITE_FIREBASE_PROJECT_ID=your-project-id
VITE_FIREBASE_STORAGE_BUCKET=your-project.appspot.com
VITE_FIREBASE_MESSAGING_SENDER_ID=123456789
VITE_FIREBASE_APP_ID=1:123456789:web:abc123def456
VITE_ENVIRONMENT=development
```

⚠️ **IMPORTANT**: Never commit `.env.local` to Git. It's already in `.gitignore`.

### Step 2: Test Local Setup

Open `index.html` in your browser:
- You should see the login screen
- Try creating an account with email
- Verify email (check your inbox)
- After verification, you can use the app

---

## Phase 3: Deploy to Firebase Hosting

### Step 1: Set Up Firebase CLI

```bash
# Install Firebase CLI globally
npm install -g firebase-tools

# Log in to Firebase (opens browser for authentication)
firebase login

# List your Firebase projects
firebase list
```

### Step 2: Initialize Firebase in Your Project

```bash
cd /home/user/Pub

# Initialize Firebase (select your project when prompted)
firebase init hosting

# When prompted:
# - Public directory: . (current directory)
# - Single-page app: No (we're using static HTML)
# - Overwrite index.html: No
```

This creates `firebase.json` and `.firebaserc` files.

### Step 3: Deploy to Firebase Hosting

```bash
firebase deploy
```

Your app will be live at: `https://your-project.web.app`

---

## Phase 4: Configure GitHub Deployment

### Step 1: Generate Firebase CI Token

This token allows GitHub Actions to deploy automatically:

```bash
firebase login:ci
```

This will:
1. Open browser for authentication
2. Return a CI token after successful login
3. Copy this token (you'll use it next)

⚠️ **IMPORTANT**: Keep this token secret. Treat it like a password.

### Step 2: Add GitHub Secrets

1. Go to your GitHub repository: `kyretium/pub`
2. Navigate to **Settings > Secrets and variables > Actions**
3. Click **"New repository secret"** and add:

| Secret Name | Value |
|---|---|
| `FIREBASE_TOKEN` | (paste the CI token from step 1) |
| `FIREBASE_PROJECT_ID` | your-project-id |

4. Click **"Add secret"** for each

### Step 3: Enable Branch Protection

1. Go to **Settings > Branches**
2. Click **"Add rule"** under Branch protection rules
3. Branch name pattern: `main` or `master`
4. Enable:
   - ✅ Require a pull request before merging
   - ✅ Require status checks to pass
   - ✅ Require branches to be up to date before merging
5. Click **"Create"**

Now every push to main will automatically deploy!

---

## Phase 5: Configure Firestore Security Rules

### Step 1: Access Firestore Rules

1. Go to Firebase Console
2. Navigate to **Build > Firestore Database**
3. Click **"Rules"** tab

### Step 2: Deploy Security Rules

Replace default rules with comprehensive security rules:

```javascript
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    
    // Helper functions
    function isOwner(tripId) {
      return get(/databases/$(database)/documents/trips/$(tripId)).data.ownerId == request.auth.uid;
    }
    
    function hasAccess(tripId) {
      let trip = get(/databases/$(database)/documents/trips/$(tripId)).data;
      return request.auth.uid == trip.ownerId || 
             request.auth.uid in trip.sharedWith;
    }
    
    function hasEditAccess(tripId) {
      let trip = get(/databases/$(database)/documents/trips/$(tripId)).data;
      return request.auth.uid == trip.ownerId || 
             trip.sharedWith.get(request.auth.uid) == 'editor';
    }
    
    function isValidEmail(email) {
      return email.matches('^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\\.[a-zA-Z]{2,}$');
    }
    
    // Trips collection
    match /trips/{tripId} {
      allow read: if hasAccess(tripId);
      allow create: if request.auth != null &&
                       request.resource.data.ownerId == request.auth.uid &&
                       request.resource.data.name is string &&
                       request.resource.data.name.size() > 0 &&
                       request.resource.data.name.size() <= 255;
      allow update: if hasEditAccess(tripId) &&
                       request.resource.data.ownerId == resource.data.ownerId;
      allow delete: if isOwner(tripId);
      
      // Expenses subcollection
      match /expenses/{expenseId} {
        allow read: if hasAccess(tripId);
        allow create: if hasEditAccess(tripId) &&
                         request.resource.data.createdBy == request.auth.uid &&
                         request.resource.data.amount > 0;
        allow update: if resource.data.createdBy == request.auth.uid ||
                         isOwner(tripId);
        allow delete: if resource.data.createdBy == request.auth.uid ||
                         isOwner(tripId);
      }
    }
    
    // User profiles
    match /users/{userId} {
      allow read: if request.auth.uid == userId;
      allow write: if request.auth.uid == userId &&
                      request.resource.data.email is string &&
                      isValidEmail(request.resource.data.email);
    }
    
    // Cloud Storage rules for photos
    match /storage/trips/{tripId}/photos/{photoId} {
      allow read: if hasAccess(tripId);
      allow write: if hasEditAccess(tripId) &&
                      request.resource.contentType.matches('image/.*') &&
                      request.resource.size < 5242880; // 5MB max
    }
  }
}
```

3. Click **"Publish"**

### Step 3: Test Rules

1. Go to **Firestore Database > Rules > Rules Playground**
2. Test read/write scenarios:
   - Authenticated user creating trip
   - Non-owner trying to edit (should fail)
   - Viewer trying to add expense (should fail)

---

## Phase 6: Configure Cloud Storage Rules

1. Go to **Build > Storage > Rules**
2. Replace default rules:

```javascript
rules_version = '2';
service firebase.storage {
  match /b/{bucket}/o {
    // Allow authenticated users to upload images
    match /trips/{tripId}/photos/{photoId} {
      allow read: if request.auth != null;
      allow write: if request.auth != null &&
                      request.resource.contentType.matches('image/.*') &&
                      request.resource.size < 5242880; // 5MB max
      allow delete: if request.auth != null;
    }
  }
}
```

3. Click **"Publish"**

---

## Phase 7: Production Security Checklist

Before going live, verify:

- [ ] Firebase project created
- [ ] Firestore Database enabled with security rules
- [ ] Authentication enabled (Email/Password)
- [ ] Cloud Storage enabled with rules
- [ ] Web app created and config obtained
- [ ] `.env.local` created locally (NOT committed)
- [ ] `.env.example` with placeholders committed
- [ ] `.gitignore` includes `.env.local` and `.firebase/`
- [ ] GitHub Secrets configured (FIREBASE_TOKEN, FIREBASE_PROJECT_ID)
- [ ] Branch protection rules enabled on main
- [ ] GitHub Actions workflow created and tested
- [ ] Firestore rules tested and published
- [ ] Cloud Storage rules published
- [ ] Email verification enabled in Firebase Auth
- [ ] reCAPTCHA v3 (optional) configured for bot protection
- [ ] Firebase Hosting deployed
- [ ] Production URL verified working

---

## Phase 8: Ongoing Monitoring

### Monitor Firebase Usage

1. **Firestore Quota**: Go to **Project Settings > Usage and billing > Quotas**
   - Monitor read/write operations
   - Free tier: 50K reads, 20K writes daily

2. **Storage Usage**: Go to **Storage > Usage**
   - Monitor total storage used
   - Delete old photos after 90 days

### Enable Alerts

1. Go to **Project Settings > Notifications**
2. Enable email alerts for:
   - Quota exceeded warnings
   - Authentication issues
   - Unusual activity

### Review Audit Logs

1. Go to **Build > Firestore Database > Indexes**
2. Monitor slow queries
3. Optimize rules if needed

---

## Troubleshooting

### Problem: "API key not valid" error

**Solution**: 
- Verify Firebase config in `.env.local` matches project settings
- Restart development server after changing `.env.local`
- Check API key in Firebase Console > Project Settings

### Problem: Authentication fails

**Solution**:
- Verify Authentication is enabled in Firebase
- Check email verification is required
- Test with new email address

### Problem: Firestore rules reject all operations

**Solution**:
- Ensure user is authenticated (logged in)
- Check `ownerId` field is set correctly
- Verify rule syntax in Rules Playground

### Problem: Deployment fails from GitHub Actions

**Solution**:
- Verify `FIREBASE_TOKEN` is correctly set in GitHub Secrets
- Run `firebase login:ci` again and update token
- Check `FIREBASE_PROJECT_ID` matches your project
- Review GitHub Actions logs for specific error

---

## Support & Resources

- [Firebase Console](https://console.firebase.google.com)
- [Firebase Documentation](https://firebase.google.com/docs)
- [Firestore Security Rules](https://firebase.google.com/docs/firestore/security/get-started)
- [Firebase Hosting](https://firebase.google.com/docs/hosting)

---

## Next Steps

1. ✅ Complete Firebase setup
2. ✅ Configure local development
3. ✅ Deploy to Firebase Hosting
4. ✅ Set up GitHub Actions
5. 📝 Update `index.html` with Firebase SDK code
6. 🧪 Test authentication and real-time sync
7. 🚀 Production deployment
