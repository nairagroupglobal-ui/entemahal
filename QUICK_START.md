# ⚡ Quick Start Checklist

Use this checklist to ensure everything is properly configured before using the application.

## Pre-Launch Checklist

### ✅ System Requirements
- [ ] Node.js v18+ installed (`node --version`)
- [ ] npm installed (`npm --version`)
- [ ] Web browser (Chrome, Firefox, Safari, or Edge)
- [ ] MongoDB account or local MongoDB installed

### ✅ Project Setup
- [ ] Repository cloned/downloaded
- [ ] Navigate to project folder: `cd entemahal`
- [ ] Dependencies installed: `npm install`
- [ ] `.env.local` file created with variables

### ✅ Environment Configuration
- [ ] `MONGODB_URI` set and tested
  - [ ] MongoDB Atlas account created (OR)
  - [ ] Local MongoDB installed and running
- [ ] Connection string is valid
- [ ] Database user created with correct permissions
- [ ] (Optional) Stripe keys added if using card payments

### ✅ Development Server
- [ ] Run `npm run dev`
- [ ] Server shows "Ready in X seconds"
- [ ] No error messages in terminal
- [ ] Shows "http://localhost:3000" is available

### ✅ Browser Access
- [ ] Open http://localhost:3000
- [ ] Dashboard loads successfully
- [ ] No console errors (F12 to check)
- [ ] Page displays in browser

### ✅ Database Connection
- [ ] Record a test payment
- [ ] Payment submitted without errors
- [ ] Receipt displays with receipt number
- [ ] Check MongoDB Atlas/local DB - record appears

### ✅ Core Features Working
- [ ] Dashboard tab loads
- [ ] New Payment tab accessible
- [ ] Payment form has all fields
- [ ] Payment History shows recorded payments
- [ ] Receipt displays and prints preview shows

### ✅ Mobile/PWA Testing (Optional)
- [ ] Test on mobile device or emulator
- [ ] Responsive layout works
- [ ] All buttons clickable
- [ ] Forms are usable on mobile
- [ ] Can install app on mobile (Add to Home Screen)

---

## Configuration Matrix

### MongoDB Atlas Setup
```
┌─────────────────────────────────────────────────┐
│ 1. Create Free Account at mongodb.com/atlas     │
│ 2. Create New Project                           │
│ 3. Create Free Cluster (M0)                     │
│ 4. Create Database User (masjid_user)          │
│ 5. Whitelist Your IP (0.0.0.0/0 for testing)  │
│ 6. Copy Connection String                       │
│ 7. Add to .env.local MONGODB_URI               │
│ 8. Test Connection                              │
└─────────────────────────────────────────────────┘
```

### Local MongoDB Setup
```
┌─────────────────────────────────────────────────┐
│ 1. Download MongoDB Community Edition           │
│ 2. Run installer and complete setup             │
│ 3. Start MongoDB service                        │
│ 4. Verify connection with mongosh               │
│ 5. Set MONGODB_URI=mongodb://localhost:27017    │
│ 6. Restart dev server                           │
│ 7. Test Connection                              │
└─────────────────────────────────────────────────┘
```

---

## Verification Steps

### Step 1: Verify Installation
```bash
# Terminal check
node --version          # Should show v18 or higher
npm --version           # Should show 8 or higher
```

### Step 2: Verify Project Files
```bash
# Should see these files
ls -la               # List all files
cat .env.local       # Check environment variables
cat package.json     # Check dependencies
```

### Step 3: Verify Server Startup
```bash
# In terminal
npm run dev

# Should see:
# ▲ Next.js 16.2.4 (Turbopack)
# - Local:         http://localhost:3000
# ✓ Ready in X.Xs
```

### Step 4: Verify Database Connection
```bash
# Test from browser:
# 1. Go to http://localhost:3000
# 2. Try to record a payment
# 3. Should save without error
# 4. Check MongoDB Dashboard for record
```

### Step 5: Verify API Endpoints
```bash
# In browser console or terminal:
curl http://localhost:3000/api/payments

# Should return empty array (no payments yet) or existing payments
```

---

## Troubleshooting Quick Reference

### Problem: "Cannot find module 'mongoose'"
**Solution:**
```bash
npm install mongoose stripe jspdf react-to-print
```

### Problem: ECONNREFUSED MongoDB
**Solution:**
- Check MONGODB_URI in `.env.local`
- Verify MongoDB is running: `brew services list` (Mac)
- Restart dev server: `npm run dev`

### Problem: Port 3000 Already in Use
**Solution:**
```bash
# Find process using port 3000
lsof -i :3000

# Kill process
kill -9 <PID>

# Or use different port
npm run dev -- -p 3001
```

### Problem: Styles Not Loading
**Solution:**
- Clear browser cache: Ctrl+Shift+Delete
- Clear Next.js cache: `rm -rf .next`
- Restart server: `npm run dev`

### Problem: "Cannot GET /"
**Solution:**
- Verify server is running
- Check URL is exactly http://localhost:3000
- Check no errors in terminal
- Try hard refresh: Ctrl+Shift+R

---

## Feature Test Scenarios

### Test 1: Record and View Payment
```
Expected Flow:
1. Click "New Payment"
2. Fill in donor details:
   - Name: "Test Donor"
   - Phone: "555-0100"
   - Amount: "25"
   - Type: "Donation"
   - Method: "Cash"
3. Click "Record Payment"
4. Receipt displays with RCP-XXXXXX number
5. View receipt shows all details correctly
```

### Test 2: Print Receipt
```
Expected Flow:
1. After recording payment (or from history)
2. Click "Print Receipt" button
3. Print dialog opens
4. Can select printer or "Save as PDF"
5. Receipt prints or saves successfully
6. All information is readable
```

### Test 3: Payment History
```
Expected Flow:
1. Click "Payment History"
2. Table displays with recorded payments
3. Shows receipt number, name, amount, type, method, date
4. Total amount calculated correctly
5. Click "View" to see receipt
```

### Test 4: Dashboard Statistics
```
Expected Flow:
1. Go to Dashboard tab
2. Cards show:
   - Total Collected: Correct sum
   - Total Payments: Correct count
   - Today's Collection: Correct sum for today
3. Numbers update after new payment recorded
```

### Test 5: Data Persistence
```
Expected Flow:
1. Record a payment
2. Refresh page (F5)
3. Payment still visible in history
4. Dashboard stats not reset
5. Confirms data saved to database
```

---

## Performance Baseline

### Expected Load Times
- Dashboard: < 1 second
- Payment Form: < 500ms
- Payment History: < 2 seconds (with 100 records)
- Receipt Print: < 500ms

### Expected File Sizes
- Initial page load: ~50KB
- Total assets: ~200KB
- Database record: ~500 bytes

---

## Common MongoDB Connection Strings

### MongoDB Atlas (Cloud)
```
mongodb+srv://username:password@cluster0.mongodb.net/masjid_operations?retryWrites=true&w=majority
```

### Local MongoDB (Default)
```
mongodb://localhost:27017/masjid_operations
```

### Local MongoDB (Auth Enabled)
```
mongodb://username:password@localhost:27017/masjid_operations?authSource=admin
```

### Docker MongoDB
```
mongodb://mongodb:27017/masjid_operations
```

---

## Support Resources

### Documentation Files
- **README.md** - Project overview and features
- **SETUP_COMPLETE.md** - Setup confirmation and status
- **FEATURE_GUIDE.md** - Detailed feature documentation
- **DATABASE_GUIDE.md** - Database setup and management
- **This file** - Quick start checklist

### External Resources
- [Next.js Documentation](https://nextjs.org/docs)
- [MongoDB Documentation](https://docs.mongodb.com)
- [React Documentation](https://react.dev)
- [Tailwind CSS Docs](https://tailwindcss.com)

### Terminal Commands Reference
```bash
npm run dev              # Start development server
npm run build            # Build for production
npm start               # Start production server
npm run lint            # Check code quality
npm install             # Install dependencies
npm cache clean --force # Clear npm cache
```

---

## Next Steps After Setup

### Immediate (First Hour)
1. ✅ Run dev server
2. ✅ Access application in browser
3. ✅ Record test payment
4. ✅ Print test receipt
5. ✅ Verify database save

### Today (First Day)
1. ✅ Invite team to test
2. ✅ Get feedback on UI
3. ✅ Test on mobile device
4. ✅ Verify all features
5. ✅ Record actual data

### This Week
1. ✅ Set up MongoDB backups
2. ✅ Train treasure/admins
3. ✅ Create user guide
4. ✅ Customize branding (optional)
5. ✅ Plan deployment

### Next Sprint (Optional)
1. ☐ Add Stripe integration
2. ☐ Implement user authentication
3. ☐ Create monthly reports
4. ☐ Deploy to production
5. ☐ Set up automated backups

---

## Success Criteria

You'll know the setup is complete when:

- ✅ Dev server runs without errors
- ✅ Application loads in browser
- ✅ Can record payments
- ✅ Receipts print/save successfully
- ✅ Data persists after refresh
- ✅ History displays correctly
- ✅ Statistics update in real-time
- ✅ Works on mobile devices
- ✅ No console errors
- ✅ Database connected and working

**If all above are ✅, you're ready to go!**

---

## Getting Help

### If Something Breaks
1. Check error message in browser console (F12)
2. Check terminal for server errors
3. Review relevant guide (DATABASE_GUIDE.md, FEATURE_GUIDE.md)
4. Try restarting: `npm run dev`
5. Clear cache: `rm -rf .next && npm run dev`

### Reporting Issues
Document:
- What you were doing
- What error appeared
- Steps to reproduce
- Screenshots if helpful

---

## Version Information

```
Next.js:        16.2.4
React:          19
Node.js:        18+ required
TypeScript:     5.x
Tailwind CSS:   3.x
MongoDB:        Latest
```

---

**Last Updated:** April 2026
**Setup Status:** ✅ COMPLETE
**Ready for Use:** ✅ YES

🎉 **Congratulations! Your Masjid Operations Management PWA is ready!**
