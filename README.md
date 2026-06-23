# Static Website Hosting — S3 + CloudFront

> My first AWS cloud project — a personal portfolio website deployed on Amazon S3 and served globally via CloudFront CDN with HTTPS.

🌐 **Live:** https://d2wrsh5icek6ta.cloudfront.net

---

## What I Built

I hosted my personal portfolio website — **Anjum, Cloud Support Engineer based in Dubai, UAE** — entirely on AWS without any traditional web server. The site runs on Amazon S3 as the storage layer and CloudFront as the delivery layer, meaning visitors anywhere in the world get the page served from the nearest AWS Edge Location instead of a single server.

---

## Architecture

```
Visitor's Browser
       │
       ▼
┌─────────────────────────┐
│   Amazon CloudFront     │  ← CDN — 400+ Edge Locations worldwide
│   HTTPS enforced        │    Caches content close to visitors
└────────────┬────────────┘
             │ Cache miss (first request)
             ▼
┌─────────────────────────┐
│     Amazon S3 Bucket    │  ← Origin — stores all website files
│  Static website hosting │    index.html, CSS, assets
└─────────────────────────┘
```

| Layer | Service | What it does |
|-------|---------|-------------|
| Storage | Amazon S3 | Stores and serves the HTML, CSS, and assets |
| Delivery | Amazon CloudFront | Caches and delivers content from Edge Locations |
| Security | CloudFront HTTPS | Enforces secure connections (HTTP → HTTPS redirect) |
| Access | S3 Bucket Policy | Allows public read access to website files |

---

## What the Site Contains

- My name and role: **AWS Cloud Practitioner | Aspiring Cloud Support Engineer**
- Location: Dubai, UAE
- AWS skills: EC2, S3, VPC, IAM, CloudFront, CloudWatch
- Certifications: AWS Certified Cloud Practitioner (CLF-C02)
- Contact: LinkedIn, GitHub, Email

---

## How I Deployed It

### 1. Created the S3 bucket

Created a new S3 bucket in the AWS Console with a unique name matching my project.

```bash
aws s3api create-bucket \
  --bucket my-portfolio-bucket \
  --region us-east-1
```

### 2. Enabled static website hosting

Turned on static website hosting in the bucket settings and set `index.html` as the entry point.

```bash
aws s3 website s3://my-portfolio-bucket/ \
  --index-document index.html \
  --error-document error.html
```

### 3. Wrote the bucket policy

Disabled "Block all public access" in the bucket settings, then attached this bucket policy to allow public read:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "PublicReadGetObject",
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::my-portfolio-bucket/*"
    }
  ]
}
```

### 4. Uploaded my website files

```bash
aws s3 sync ./website s3://my-portfolio-bucket/ --delete
```

### 5. Created the CloudFront distribution

In the AWS Console:
- **Origin domain** → pointed to the S3 static website endpoint
- **Viewer protocol policy** → Redirect HTTP to HTTPS
- **Default root object** → `index.html`
- Deployed and waited ~10 minutes for the distribution to go live

### 6. Tested the live URL

Opened `https://d2wrsh5icek6ta.cloudfront.net` in the browser — site loaded globally with HTTPS confirmed.

---

## Updating the Site

Whenever I make changes to the website files:

```bash
# Push new files to S3
aws s3 sync ./website s3://my-portfolio-bucket/ --delete

# Clear the CloudFront cache so changes appear immediately
aws cloudfront create-invalidation \
  --distribution-id YOUR_DISTRIBUTION_ID \
  --paths "/*"
```

---

## What I Learned

- How Amazon S3 can serve a website without any EC2 instance or web server
- The difference between an S3 bucket endpoint and a CloudFront distribution endpoint
- How CloudFront Edge Locations cache content globally to reduce latency for visitors worldwide
- How the **Shared Responsibility Model** applies here — AWS secures the infrastructure, I am responsible for the bucket policy and access configuration
- Why **HTTP → HTTPS redirect** matters and how CloudFront enforces it
- How cache invalidation works when deploying updates

---

## Key AWS Concepts Demonstrated

| Concept | How it applies here |
|---------|-------------------|
| Serverless hosting | No EC2, no web server — S3 serves the files directly |
| Content Delivery Network | CloudFront caches at Edge Locations for low latency |
| Least privilege | Bucket policy grants only `s3:GetObject`, nothing more |
| HTTPS enforcement | CloudFront redirects all HTTP traffic to HTTPS |
| Infrastructure cost | Entire setup costs under $1/month for a personal site |

---

## Estimated Monthly Cost

| Service | Usage | Cost |
|---------|-------|------|
| S3 storage | < 1 MB of HTML/CSS | ~$0.00 |
| S3 requests | Low traffic | ~$0.00 |
| CloudFront | First 1 TB + 10M requests free | ~$0.00 |
| **Total** | | **< $1/month** |

---

## About Me

**Anjum** — AWS Cloud Practitioner | Aspiring Cloud Support Engineer | Dubai, UAE

- Certified: AWS Cloud Practitioner (CLF-C02)
- Skills: EC2, S3, VPC, IAM, CloudFront, CloudWatch
- 🌐 Portfolio: https://d2wrsh5icek6ta.cloudfront.net
- 💼 LinkedIn: [linkedin.com/in/anjum-mohammed](https://www.linkedin.com/in/anjum-mohammed-3a36141a6/)
- 🐙 GitHub: [github.com/anjumasif123](https://github.com/anjumasif123)
- 📧 Email: anjumasif060@gmail.com

---

## Resources I Used

- [Amazon S3 Static Website Hosting](https://docs.aws.amazon.com/AmazonS3/latest/userguide/WebsiteHosting.html)
- [Amazon CloudFront Developer Guide](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/Introduction.html)
- [AWS Shared Responsibility Model](https://aws.amazon.com/compliance/shared-responsibility-model/)
