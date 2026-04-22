# Feature Guide - Masjid Operations Management PWA

## Dashboard Overview

The main dashboard displays:
- **Total Collected:** Sum of all payments recorded
- **Total Payments:** Count of all payment records
- **Today's Collection:** Sum of payments recorded today

### Dashboard Actions
- "Record New Payment" button → Takes you to payment form
- "View Payment History" button → Shows all payments

---

## Payment Management

### Recording a New Payment

#### Step 1: Access Payment Form
- Click "💳 New Payment" tab in navigation

#### Step 2: Fill Payment Form
```
Donor Name:    [Text input] (Required)
               Example: "Ahmed Khan"
               
Phone:         [Text input] (Required)
               Example: "+1-555-0100" or "555-0100"
               
Amount:        [Number input] (Required)
               Example: 50
               Accept decimals: 50.75
               
Payment Type:  [Dropdown] (Required)
               Options:
               - Donation (general contribution)
               - Zakat (Islamic mandatory alms)
               - Sadaqah (voluntary charity)
               - Maintenance (facility maintenance)
               
Payment Method: [Dropdown] (Required)
               Options:
               - Cash (physical money)
               - Card (credit/debit card)
               - Bank Transfer (direct transfer)
               
Notes:         [Text area] (Optional)
               Example: "For Ramadan food packages"
               Maximum length: depends on database limit
```

#### Step 3: Submit
- Click "Record Payment" button
- Form validates all required fields
- System automatically generates receipt number (format: RCP-000001)
- Redirects to receipt display

#### Payment Stored In Database
```javascript
{
  _id: ObjectId,
  donor_name: "Ahmed Khan",
  donor_phone: "+1-555-0100",
  amount: 50,
  payment_type: "donation",
  payment_method: "cash",
  receipt_number: "RCP-000001",
  notes: "For Ramadan food packages",
  created_at: "2026-04-22T10:30:00Z",
  updated_at: "2026-04-22T10:30:00Z"
}
```

---

## Receipt Management

### Receipt Display Features

Each receipt shows:
- **Header:** "MASJID OPERATIONS" title
- **Receipt Number:** Unique identifier (RCP-XXXXXX)
- **Date & Time:** When payment was recorded
- **Donor Information:**
  - Name
  - Phone number
- **Payment Details:**
  - Type (Donation/Zakat/Sadaqah/Maintenance)
  - Method (Cash/Card/Bank Transfer)
- **Amount:** Total payment amount displayed prominently
- **Notes:** Any additional information (if provided)
- **Footer:** Thank you message with Islamic blessing

### Printing Receipt

#### Desktop
1. Open receipt
2. Click "🖨️ Print Receipt" button
3. Choose printer or save as PDF
4. Paper size: Any (auto-fits)
5. Margins: Automatic
6. Colors: Include colors for professional look

#### Mobile
1. Open receipt on mobile device
2. Click "🖨️ Print Receipt" button
3. Options available:
   - Print to wireless printer
   - Save as PDF to Photos/Files

#### PDF Export
- Click "Print Receipt"
- Select "Save as PDF" in printer dialog
- File saved with date/receipt number

---

## Payment History

### Viewing Payment History

#### Access History Tab
- Click "📋 Payment History" tab

#### Table Display Shows
| Column | Info |
|--------|------|
| Receipt # | Unique receipt number (RCP-XXXXXX) |
| Donor Name | Full name of donor |
| Amount | Payment amount ($) |
| Type | Donation/Zakat/Sadaqah/Maintenance |
| Method | Cash/Card/Bank Transfer |
| Date | Date payment was recorded |
| Action | "View" button for receipt |

#### Search & Filter (Future Feature)
- Currently shows all payments
- Sorted by newest first
- Real-time total calculation

#### Statistics Box
- Shows total amount collected from all records
- Updates automatically when new payments added

### View Individual Payment Receipt
1. Go to "Payment History" tab
2. Find payment in table
3. Click "View" button
4. Receipt details display
5. Click "Print Receipt" to print/save

---

## Statistics & Reporting

### Dashboard Statistics
**Real-time Calculated From Database:**

1. **Total Collected**
   - Formula: Sum of all payment amounts
   - Updates when new payment recorded
   - Display format: $XXXX.XX

2. **Total Payments**
   - Formula: Count of all payment records
   - Shows number of transactions
   - Useful for attendance/participation tracking

3. **Today's Collection**
   - Formula: Sum of payments recorded today
   - Compared with today's date
   - Helps track daily progress

### Example Calculations
```
Payments Recorded:
1. Ahmed Khan - $50 (Donation) - Today 10:00 AM
2. Fatima Ali - $25 (Zakat) - Today 11:30 AM
3. Mohammed Hassan - $100 (Maintenance) - Yesterday 2:00 PM

Dashboard Shows:
- Total Collected: $175.00
- Total Payments: 3
- Today's Collection: $75.00
```

---

## Payment Types Explained

### 1. Donation (Donation)
- General voluntary contribution
- No specific religious obligation
- Flexible amounts
- For any masjid needs

### 2. Zakat (Zakat)
- Islamic mandatory alms (2.5% of wealth)
- Required from eligible Muslims
- Specific Islamic purpose
- Track separately if needed

### 3. Sadaqah (Sadaqah)
- Voluntary charity beyond zakat
- Can be for specific purpose
- Encouraged throughout Islam
- Includes sadaqah jariyah (ongoing charity)

### 4. Maintenance (Maintenance)
- Facility maintenance fund
- Building repairs and upkeep
- Utilities (if tracked)
- General facility costs

---

## Payment Methods Explained

### 1. Cash
- Physical currency payment
- Immediate receipt
- No processing fees
- Direct deposit

### 2. Card
- Credit/Debit card payment
- Integrated with Stripe (when configured)
- Processing fee may apply
- Digital record created

### 3. Bank Transfer
- Direct bank-to-bank transfer
- Best for large amounts
- Low processing fees
- Slower processing

---

## Advanced Features (Coming Soon)

### Email Receipts
- Automatically send receipt to donor email
- Professional email template
- Searchable receipt archive

### SMS Notifications
- Send donation receipt via SMS
- Donor confirmation notification
- Monthly summary to treasurer

### Reports & Analytics
- Daily/Weekly/Monthly reports
- Income by type (Zakat/Donation/etc)
- Income by method (Cash/Card/etc)
- Top donors list
- Trends and charts

### Export Features
- Export to Excel
- Export to PDF
- Monthly statements
- Annual reports

### User Roles
- **Admin:** Full access, settings
- **Treasurer:** Record/view all payments
- **Viewer:** View-only access to reports
- **Manager:** Approve large payments

### Multi-Branch Support
- Track multiple masjid locations
- Compare branch statistics
- Consolidated reporting

---

## Keyboard Shortcuts (Coming Soon)

```
Ctrl/Cmd + N  → New Payment
Ctrl/Cmd + H  → Payment History
Ctrl/Cmd + P  → Print Receipt
Ctrl/Cmd + R  → Refresh Dashboard
```

---

## Mobile App Experience

### Installing as PWA

**Android:**
1. Open browser (Chrome recommended)
2. Press menu (3 dots)
3. Tap "Add to Home Screen"
4. App icon appears on home screen
5. Click icon to launch as app

**iOS:**
1. Open in Safari browser
2. Tap share icon (↑)
3. Tap "Add to Home Screen"
4. Give app a name
5. Tap "Add"
6. App available from home screen

### Mobile Interface Features
- Touch-friendly buttons
- Scrollable tables
- Mobile-optimized forms
- Bottom navigation for easy access
- Responsive layout

---

## Data Privacy & Security

### Data Stored
- Donor name and phone
- Payment amount and type
- Payment method
- Timestamps
- Receipt numbers

### Not Stored
- Password information (not implemented yet)
- Credit card details (handled by Stripe only)
- Personal ID numbers
- Home addresses

### Security Measures
- Environment variables for sensitive data
- MongoDB encryption ready
- HTTPS support for production
- Input validation on all forms

---

## Offline Functionality (PWA)

When installed as PWA:
- Basic interface loads offline
- List cached on device
- Payments queued when offline
- Syncs when connection restored

---

## Integration Examples

### API Request - Record Payment
```javascript
POST /api/payments
Content-Type: application/json

{
  "donor_name": "Ahmed Khan",
  "donor_phone": "+1-555-0100",
  "amount": 50,
  "payment_type": "donation",
  "payment_method": "cash",
  "notes": "For Ramadan programs"
}

Response:
{
  "_id": "507f1f77bcf86cd799439011",
  "donor_name": "Ahmed Khan",
  "donor_phone": "+1-555-0100",
  "amount": 50,
  "payment_type": "donation",
  "payment_method": "cash",
  "receipt_number": "RCP-000042",
  "notes": "For Ramadan programs",
  "created_at": "2026-04-22T10:30:00Z",
  "updated_at": "2026-04-22T10:30:00Z"
}
```

### API Request - Get Payment History
```javascript
GET /api/payments

Response:
[
  {
    "_id": "507f1f77bcf86cd799439011",
    "donor_name": "Ahmed Khan",
    "amount": 50,
    "payment_type": "donation",
    "receipt_number": "RCP-000042",
    "created_at": "2026-04-22T10:30:00Z"
  },
  ...more payments
]
```

---

## Troubleshooting Guide

### Payment Not Recorded
**Check:**
- All required fields filled (marked with *)
- Valid phone number format
- Amount is a valid number
- Check browser console for errors

**Solution:**
- Verify MongoDB connection in `.env.local`
- Check server logs in terminal
- Clear browser cache and retry

### Receipt Won't Print
**Check:**
- Click "Print Receipt" button
- Browser print dialog appears
- Have a printer configured

**Solution:**
- Check browser permissions
- Try different printer option
- Restart browser and try again

### Dashboard Statistics Wrong
**Check:**
- Refresh page to reload data
- Check all payments recorded correctly
- Verify today's date matches system

**Solution:**
- Go to Payment History tab
- Verify payment amounts
- Check payment timestamps

---

## Best Practices

### Recording Payments
1. ✅ Always get donor name
2. ✅ Always get phone number
3. ✅ Select correct payment type
4. ✅ Select correct payment method
5. ✅ Add notes for special payments

### Managing Records
1. ✅ Print receipt immediately after recording
2. ✅ Keep digital copies
3. ✅ Review history regularly
4. ✅ Backup database monthly
5. ✅ Verify amounts match physical records

### Monthly Tasks
1. ✅ Generate monthly report
2. ✅ Reconcile with physical cash/records
3. ✅ Verify all payments in system
4. ✅ Print monthly statement
5. ✅ Archive digital backups

---

## Helpful Tips

- **Tip 1:** Keep phone format consistent for easy search
- **Tip 2:** Use notes field to track donation purpose
- **Tip 3:** Monthly summaries help with transparency
- **Tip 4:** Test print function when first setting up
- **Tip 5:** Backup database before major changes

---

**Last Updated:** April 2026
**Version:** 1.0.0 Beta
