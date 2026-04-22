# Database Operations Guide

## MongoDB Setup & Management

### Option 1: MongoDB Atlas (Recommended - Cloud)

#### Benefits
- ✅ No installation required
- ✅ Free tier with 512MB storage
- ✅ Automatic backups
- ✅ Accessible from anywhere
- ✅ HTTPS encryption
- ✅ Scalable when needed

#### Setup Steps

**1. Create MongoDB Atlas Account**
- Go to https://www.mongodb.com/cloud/atlas
- Click "Sign up with Email"
- Create account with your email
- Verify email address

**2. Create Organization & Project**
- Create new organization (if prompted)
- Create new project (name it: "masjid_operations")
- Accept terms

**3. Create Cluster**
- Click "Create Deployment"
- Select "Free" tier
- Choose cloud provider (AWS, Google Cloud, Azure)
- Choose region closest to you
- Leave other settings as default
- Click "Create"
- Wait 5-10 minutes for cluster to be created

**4. Create Database User**
- Go to "Database Access"
- Click "Add New Database User"
- Username: `masjid_user`
- Password: Create strong password
- Select "Built-in Role" → "Atlas Admin"
- Click "Add User"

**5. Whitelist IP Address**
- Go to "Network Access"
- Click "Add IP Address"
- Select "Allow Access from Anywhere" (for testing only)
  - For production: Add specific IP only
- Click "Confirm"

**6. Get Connection String**
- Go to "Database"
- Click "Connect"
- Select "Drivers"
- Copy connection string
- Replace `<username>` and `<password>` with your database user credentials
- Replace `<password>` with actual password (not placeholder)

**7. Update .env.local**
```
MONGODB_URI=mongodb+srv://masjid_user:your_password_here@cluster0.mongodb.net/masjid_operations?retryWrites=true&w=majority
```

**8. Test Connection**
- Restart dev server: `npm run dev`
- Go to http://localhost:3000
- Record a test payment
- Check MongoDB Atlas → Collections to verify data

---

### Option 2: Local MongoDB (Development Only)

#### Installation

**Windows:**
```bash
# Download and install from:
# https://www.mongodb.com/try/download/community

# Or using Chocolatey:
choco install mongodb-community

# Verify installation:
mongod --version
```

**macOS:**
```bash
# Using Homebrew:
brew tap mongodb/brew
brew install mongodb-community

# Start MongoDB:
brew services start mongodb-community

# Verify:
mongosh
```

**Linux (Ubuntu/Debian):**
```bash
# Add MongoDB repository
wget -qO - https://www.mongodb.org/static/pgp/server-6.0.asc | sudo apt-key add -
echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/6.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-6.0.list
sudo apt-get update
sudo apt-get install -y mongodb-org

# Start MongoDB:
sudo systemctl start mongod

# Check status:
sudo systemctl status mongod
```

#### Configuration

**Update .env.local**
```
MONGODB_URI=mongodb://localhost:27017/masjid_operations
```

**Access MongoDB CLI**
```bash
# Connect to MongoDB:
mongosh

# List databases:
show databases

# Use masjid database:
use masjid_operations

# View collections:
show collections

# Query payments:
db.payments.find()
```

---

## Database Schema

### Payment Collection
```javascript
db.createCollection("payments", {
  validator: {
    $jsonSchema: {
      bsonType: "object",
      required: ["donor_name", "donor_phone", "amount", "payment_type", "payment_method"],
      properties: {
        _id: { bsonType: "objectId" },
        donor_name: { bsonType: "string", description: "Full name of donor" },
        donor_phone: { bsonType: "string", description: "Phone number" },
        amount: { bsonType: "double", description: "Payment amount in dollars" },
        payment_type: {
          enum: ["zakat", "sadaqah", "donation", "maintenance"],
          description: "Type of payment"
        },
        payment_method: {
          enum: ["cash", "card", "bank_transfer"],
          description: "How payment was made"
        },
        stripe_payment_id: { bsonType: ["string", "null"] },
        receipt_number: { bsonType: "string", description: "Unique receipt ID" },
        notes: { bsonType: ["string", "null"] },
        created_at: { bsonType: "date" },
        updated_at: { bsonType: "date" }
      }
    }
  }
})

// Create indexes for performance
db.payments.createIndex({ receipt_number: 1 }, { unique: true })
db.payments.createIndex({ created_at: -1 })
db.payments.createIndex({ donor_phone: 1 })
```

### Expense Collection
```javascript
db.createCollection("expenses", {
  validator: {
    $jsonSchema: {
      bsonType: "object",
      required: ["description", "amount", "category", "payment_method"],
      properties: {
        _id: { bsonType: "objectId" },
        description: { bsonType: "string" },
        amount: { bsonType: "double" },
        category: {
          enum: ["utilities", "maintenance", "staff", "supplies", "events", "other"]
        },
        payment_method: {
          enum: ["cash", "card", "bank_transfer"]
        },
        vendor_name: { bsonType: ["string", "null"] },
        receipt_image: { bsonType: ["string", "null"] },
        created_at: { bsonType: "date" },
        updated_at: { bsonType: "date" }
      }
    }
  }
})

db.expenses.createIndex({ category: 1 })
db.expenses.createIndex({ created_at: -1 })
```

### Event Collection
```javascript
db.createCollection("events", {
  validator: {
    $jsonSchema: {
      bsonType: "object",
      required: ["name", "event_type", "start_time"],
      properties: {
        _id: { bsonType: "objectId" },
        name: { bsonType: "string" },
        description: { bsonType: ["string", "null"] },
        event_type: {
          enum: ["prayer", "class", "community_event", "meeting", "other"]
        },
        start_time: { bsonType: "date" },
        end_time: { bsonType: ["date", "null"] },
        location: { bsonType: ["string", "null"] },
        expected_attendees: { bsonType: ["int", "null"] },
        actual_attendees: { bsonType: ["int", "null"] },
        created_at: { bsonType: "date" },
        updated_at: { bsonType: "date" }
      }
    }
  }
})

db.events.createIndex({ start_time: -1 })
```

---

## Backup & Restore

### MongoDB Atlas Automatic Backups
- Enabled by default for free tier
- Kept for 7 days
- Can be restored from Atlas console
- Automated at regular intervals

### Manual Backup - MongoDB Atlas

**1. Enable Backup**
- Log in to MongoDB Atlas
- Go to "Backup" section
- Verify backups are enabled

**2. Create On-Demand Backup**
- Click "Take Backup Now"
- Wait for backup to complete
- Can be restored anytime

**3. Restore from Backup**
- Go to "Backup" → "Restore"
- Select backup snapshot
- Choose new cluster or existing
- Restore process begins

### Manual Backup - Local MongoDB

**Export Data**
```bash
# Export entire database
mongoexport --uri="mongodb://localhost:27017/masjid_operations" \
  --collection=payments \
  --out=payments_backup.json

# Export all collections
mongodump --uri="mongodb://localhost:27017/masjid_operations" \
  --out=./backup_folder
```

**Import Data**
```bash
# Import collection
mongoimport --uri="mongodb://localhost:27017/masjid_operations" \
  --collection=payments \
  --file=payments_backup.json \
  --jsonArray

# Restore from backup
mongorestore --uri="mongodb://localhost:27017/masjid_operations" \
  ./backup_folder
```

### Create Monthly Backup Script

**backup.sh (Mac/Linux)**
```bash
#!/bin/bash

# Create backup folder
mkdir -p backups

# Backup MongoDB Atlas
mongodump --uri="mongodb+srv://masjid_user:password@cluster.mongodb.net/masjid_operations" \
  --out=backups/backup_$(date +%Y%m%d_%H%M%S)

# Optional: Upload to cloud storage
# aws s3 cp backups/ s3://your-bucket/masjid-backups/

echo "Backup completed successfully"
```

**backup.bat (Windows)**
```batch
@echo off
REM Create backup folder with timestamp
for /f "tokens=2-4 delims=/ " %%a in ('date /t') do (set mydate=%%c%%a%%b)
for /f "tokens=1-2 delims=/:" %%a in ('time /t') do (set mytime=%%a%%b)

set backup_dir=backups\backup_%mydate%_%mytime%
mkdir %backup_dir%

REM Backup MongoDB Atlas
mongodump --uri="mongodb+srv://masjid_user:password@cluster.mongodb.net/masjid_operations" ^
  --out=%backup_dir%

echo Backup completed successfully
```

---

## Database Monitoring

### View Database Usage
```javascript
// Connect to MongoDB
mongosh --uri "mongodb+srv://masjid_user:password@cluster.mongodb.net/masjid_operations"

// Check database stats
db.stats()

// Check collection sizes
db.payments.stats()
db.expenses.stats()
db.events.stats()

// Count documents
db.payments.countDocuments()
db.expenses.countDocuments()
db.events.countDocuments()

// Find largest documents
db.payments.find().sort({amount: -1}).limit(10)

// Find most recent payments
db.payments.find().sort({created_at: -1}).limit(10)
```

### MongoDB Atlas Monitoring
- Go to "Monitoring" tab
- View:
  - Database operations/sec
  - Network in/out
  - Storage usage
  - CPU usage
  - Memory usage

---

## Performance Optimization

### Create Indexes

**Payment Lookup Speed**
```javascript
db.payments.createIndex({ receipt_number: 1 }, { unique: true })
db.payments.createIndex({ donor_phone: 1 })
db.payments.createIndex({ created_at: -1 })
db.payments.createIndex({ payment_type: 1 })
```

**Aggregation Queries**
```javascript
// Fast monthly totals
db.payments.aggregate([
  {
    $group: {
      _id: {
        year: { $year: "$created_at" },
        month: { $month: "$created_at" }
      },
      total: { $sum: "$amount" },
      count: { $sum: 1 }
    }
  },
  { $sort: { _id.year: -1, _id.month: -1 } }
])

// By payment type
db.payments.aggregate([
  {
    $group: {
      _id: "$payment_type",
      total: { $sum: "$amount" },
      count: { $sum: 1 }
    }
  },
  { $sort: { total: -1 } }
])
```

---

## Database Queries

### Common Queries

**Get Today's Collection**
```javascript
const today = new Date();
today.setHours(0, 0, 0, 0);

db.payments.aggregate([
  {
    $match: {
      created_at: { $gte: today }
    }
  },
  {
    $group: {
      _id: null,
      total: { $sum: "$amount" },
      count: { $sum: 1 }
    }
  }
])
```

**Get Monthly Report**
```javascript
db.payments.aggregate([
  {
    $group: {
      _id: {
        year: { $year: "$created_at" },
        month: { $month: "$created_at" }
      },
      totalDonation: {
        $sum: {
          $cond: [{ $eq: ["$payment_type", "donation"] }, "$amount", 0]
        }
      },
      totalZakat: {
        $sum: {
          $cond: [{ $eq: ["$payment_type", "zakat"] }, "$amount", 0]
        }
      },
      totalSadaqah: {
        $sum: {
          $cond: [{ $eq: ["$payment_type", "sadaqah"] }, "$amount", 0]
        }
      },
      totalMaintenance: {
        $sum: {
          $cond: [{ $eq: ["$payment_type", "maintenance"] }, "$amount", 0]
        }
      },
      grandTotal: { $sum: "$amount" }
    }
  }
])
```

**Top Donors**
```javascript
db.payments.aggregate([
  {
    $group: {
      _id: "$donor_phone",
      name: { $first: "$donor_name" },
      totalDonated: { $sum: "$amount" },
      count: { $sum: 1 }
    }
  },
  { $sort: { totalDonated: -1 } },
  { $limit: 10 }
])
```

---

## Security Best Practices

### Database Security

1. **Use Strong Passwords**
   - Minimum 12 characters
   - Mix uppercase, lowercase, numbers, special characters
   - Store securely

2. **Network Security**
   - Whitelist specific IP addresses for production
   - Use VPN for remote access
   - Enable HTTPS (MongoDB Atlas default)

3. **Data Protection**
   - Enable encryption at rest (Atlas)
   - Use encryption in transit (TLS)
   - Regular backups

4. **Access Control**
   - Create separate database user for app
   - Don't use admin credentials in .env
   - Rotate passwords quarterly

5. **Environment Variables**
   - Never commit .env.local to git
   - Keep .env.local in .gitignore
   - Use .env.example for documentation

---

## Scaling the Database

### When to Scale Up

- Free tier: ~512MB storage (good for testing)
- M0 cluster: For 1-100 records
- M2/M5 cluster: For 1000+ records or production use
- Dedicated clusters: For enterprise deployment

### Upgrade Process
1. Go to MongoDB Atlas
2. Select cluster
3. Click "Cluster Tier"
4. Select new tier
5. Confirm upgrade
5. Downtime: Usually 2-5 minutes

---

## Troubleshooting

### Connection Refused
```
Error: connect ECONNREFUSED
Solution:
- Check MONGODB_URI in .env.local
- Verify MongoDB is running (local)
- Check internet connection (Atlas)
- Whitelist your IP (Atlas)
- Verify username/password
```

### Unique Key Violation
```
Error: duplicate key error
Solution:
- Check receipt_number uniqueness
- Clear broken records:
  db.payments.deleteMany({ receipt_number: null })
```

### Out of Storage Space
```
Error: Quota exceeded
Solution:
- Upgrade cluster tier (Atlas)
- Delete old test data
- Implement data retention policy
```

### Slow Queries
```
Solution:
- Create indexes on frequently queried fields
- Use MongoDB Compass to analyze queries
- Limit returned fields: db.payments.find({}, {amount: 1, date: 1})
- Use aggregation pipeline for complex queries
```

---

## Maintenance Schedule

### Weekly
- [ ] Review payment records
- [ ] Check for any errors

### Monthly
- [ ] Create full backup
- [ ] Review database statistics
- [ ] Verify all transactions

### Quarterly
- [ ] Rotate database password
- [ ] Review access logs (Atlas)
- [ ] Test backup restoration

### Annually
- [ ] Optimize indexes
- [ ] Archive old data
- [ ] Review security settings
- [ ] Plan capacity for next year

---

**Last Updated:** April 2026
**Version:** 1.0.0
