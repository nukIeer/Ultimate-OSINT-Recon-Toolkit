# 🕵️ Ultimate OSINT Recon Toolkit

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](https://opensource.org/licenses/MIT)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](http://makeapullrequest.com)
[![OSINT](https://img.shields.io/badge/OSINT-Reconnaissance-red.svg)](https://github.com/topics/osint)

> A curated collection of OSINT snippets for email reconnaissance, subdomain enumeration, and social media profiling. Designed for bug bounty hunters and security researchers needing fast, effective open-source intelligence techniques.

## 📋 Table of Contents

- [Features](#-features)
- [Quick Start](#-quick-start)
- [Email Reconnaissance](#-email-reconnaissance)
- [Subdomain Enumeration](#-subdomain-enumeration)
- [Social Media Profiling](#-social-media-profiling)
- [Advanced Techniques](#-advanced-techniques)
- [Tools & Dependencies](#-tools--dependencies)
- [Legal Disclaimer](#-legal-disclaimer)
- [Contributing](#-contributing)

## ⚡ Features

- **Email Intelligence**: Breach detection, format validation, and corporate email enumeration
- **Subdomain Discovery**: Multiple enumeration techniques and passive reconnaissance
- **Social Media OSINT**: Profile discovery across platforms and metadata extraction
- **Automation Ready**: Copy-paste snippets for immediate use
- **Bug Bounty Focused**: Techniques specifically chosen for security research

## 🚀 Quick Start

```bash
# Clone essential tools
git clone https://github.com/laramies/theHarvester.git
git clone https://github.com/aboul3la/Sublist3r.git
pip3 install sherlock-project holehe
```

## 📧 Email Reconnaissance

### Breach Database Lookup
```bash
# Check if email appears in known breaches
curl -s "https://haveibeenpwned.com/api/v3/breachedaccount/target@domain.com" \
  -H "hibp-api-key: YOUR_API_KEY"

# Alternative with holehe
holehe target@domain.com
```

### Email Format Discovery
```bash
# Common corporate email formats
echo "target.com" | \
awk '{print "firstname.lastname@"$1"\nfirstname_lastname@"$1"\ninitial.lastname@"$1"\nflastname@"$1}'

# TheHarvester email enumeration
python3 theHarvester.py -d target.com -l 500 -b google,bing,yahoo
```

### Email Validation
```bash
# SMTP validation without sending
telnet mx.target.com 25
HELO test.com
MAIL FROM: test@test.com
RCPT TO: target@target.com
```

### Corporate Directory Mining
```bash
# LinkedIn email pattern discovery
site:linkedin.com/in "target.com" "@target.com"

# GitHub commit email extraction
site:github.com "@target.com"

# Google dorking for email leaks
"@target.com" filetype:xlsx OR filetype:csv OR filetype:txt
```

## 🌐 Subdomain Enumeration

### Passive Enumeration
```bash
# Certificate transparency logs
curl -s "https://crt.sh/?q=%25.target.com&output=json" | \
jq -r '.[].name_value' | sort -u

# DNS enumeration with amass
amass enum -passive -d target.com -config config.ini

# Sublist3r passive scan
python3 sublist3r.py -d target.com -o subdomains.txt
```

### Active Enumeration
```bash
# Subdomain brute force
gobuster dns -d target.com -w /usr/share/wordlists/SecLists/Discovery/DNS/subdomains-top1million-5000.txt

# DNS zone transfer test
dig axfr @ns1.target.com target.com

# Wildcard detection and bypass
dig *.target.com
```

### Subdomain Validation
```bash
# Live subdomain detection
cat subdomains.txt | httprobe -c 50 | tee live_subdomains.txt

# Port scanning live subdomains
nmap -T4 -iL live_subdomains.txt -p 80,443,8080,8443 --open
```

### Advanced Subdomain Techniques
```bash
# ASN enumeration for IP ranges
whois -h whois.radb.net '!gAS32934' | grep -E '^route:'

# Reverse DNS on IP ranges
for ip in $(seq 1 254); do 
  host 192.168.1.$ip | grep "domain name pointer"
done

# GitHub subdomain mining
curl -s "https://api.github.com/search/code?q=*.target.com+extension:json" | \
jq -r '.items[].repository.clone_url'
```

## 👥 Social Media Profiling

### Username Enumeration
```bash
# Sherlock - username search across platforms
sherlock target_username

# Manual platform checking
platforms=(
  "twitter.com"
  "instagram.com" 
  "facebook.com"
  "linkedin.com/in"
  "github.com"
  "reddit.com/user"
)

for platform in "${platforms[@]}"; do
  curl -s -I "https://$platform/target_username" | grep "HTTP"
done
```

### Social Media Intelligence
```bash
# Twitter advanced search (manual)
# from:username since:2023-01-01 until:2023-12-31
# "sensitive keyword" from:username

# Instagram metadata extraction
exiftool downloaded_image.jpg

# LinkedIn company employee enumeration
site:linkedin.com/in "Company Name" "Current"
```

### Profile Cross-Reference
```bash
# Email to social media correlation
grep -i "target@domain.com" social_media_dumps/*.txt

# Phone number OSINT
# Use truecaller, whitepages API, or manual verification

# Image reverse search automation
curl -X POST "https://www.google.com/searchbyimage/upload" \
  -F "encoded_image=@profile_photo.jpg"
```

## 🔍 Advanced Techniques

### Google Dorking Arsenal
```bash
# Sensitive file discovery
site:target.com filetype:pdf "confidential"
site:target.com intitle:"index of" "backup"
site:target.com ext:sql | ext:db | ext:dbf | ext:mdb

# Login page discovery  
site:target.com inurl:admin | inurl:login | inurl:panel

# Error page information disclosure
site:target.com "sql syntax near" | "syntax error has occurred"
```

### Metadata Extraction
```bash
# EXIF data from images
exiftool -r -ext jpg -ext png /path/to/images/

# Document metadata extraction
exiftool document.pdf | grep -i "author\|creator\|producer"

# Website technology fingerprinting
whatweb target.com
wappalyzer target.com
```

### DNS Intelligence
```bash
# Historical DNS records
curl -s "https://api.securitytrails.com/v1/domain/target.com/subdomains" \
  -H "APIKEY: YOUR_API_KEY"

# DNS TXT record enumeration
dig TXT target.com +short

# SPF record analysis for email servers
dig TXT target.com | grep -i spf
```

## 🛠️ Tools & Dependencies

### Essential Tools
```bash
# Core OSINT toolkit
sudo apt update && sudo apt install -y \
  curl wget git python3 python3-pip \
  nmap gobuster dnsutils whois \
  exiftool jq

# Python tools
pip3 install requests beautifulsoup4 shodan

# Specialized OSINT tools
go install github.com/projectdiscovery/amass/v3/cmd/amass@latest
go install github.com/tomnomnom/httprobe@latest
```

### API Keys Required
- **Have I Been Pwned**: Email breach checking
- **SecurityTrails**: Historical DNS data
- **Shodan**: Device and service enumeration
- **VirusTotal**: Domain/IP reputation checking

### Browser Extensions
- **Wappalyzer**: Technology detection
- **BuiltWith**: Technology profiling  
- **OSINT Browser Extension**: Quick OSINT lookups

## ⚖️ Legal Disclaimer

**IMPORTANT**: This toolkit is intended for authorized security testing and research purposes only.

- ✅ **Authorized**: Bug bounty programs, penetration testing with written permission
- ✅ **Legal**: Educational research, security awareness, defensive purposes
- ❌ **Prohibited**: Unauthorized access, stalking, harassment, illegal surveillance

**Always ensure you have explicit permission before conducting reconnaissance on any target. Respect privacy, follow responsible disclosure, and comply with applicable laws and regulations.**

## 🤝 Contributing

Contributions are welcome! Please follow these guidelines:

1. **Fork** the repository
2. **Create** a feature branch (`git checkout -b feature/AmazingTechnique`)
3. **Test** your snippets thoroughly
4. **Commit** your changes (`git commit -m 'Add amazing OSINT technique'`)
5. **Push** to the branch (`git push origin feature/AmazingTechnique`)
6. **Open** a Pull Request

### Contribution Guidelines
- Include clear descriptions and use cases
- Test all code snippets before submission
- Follow responsible disclosure principles
- Maintain professional and ethical standards

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🔗 Related Resources

- [OSINT Framework](https://osintframework.com/)
- [Awesome OSINT](https://github.com/jivoi/awesome-osint)
- [Bug Bounty Platforms](https://github.com/EdOverflow/bugbounty-cheatsheet)

---

**⭐ Star this repository if you find it useful!**

*Remember: With great power comes great responsibility. Use these techniques ethically and legally.*
