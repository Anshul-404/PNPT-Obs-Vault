# Note:-
- Some things are modified because there were changes in tools causing failures
```bash
#!/bin/bash

# Use the first argument as the domain name
domain=$1
# Define colors
RED="\033[1;31m"
RESET="\033[0m"

# Define directories
base_dir="~/$domain"
info_path="$base_dir/info"
subdomain_path="$base_dir/subdomains"
screenshot_path="$base_dir/screenshots"

# Create directories if they don't exist
for path in "$info_path" "$subdomain_path" "$screenshot_path"; do
    if [ ! -d "$path" ]; then
        mkdir -p "$path"
        echo "Created directory: $path"
    fi
done

echo -e "${RED} [+] Checking who it is ... ${RESET}"
whois "$domain" > "$info_path/whois.txt"

echo -e "${RED} [+] Launching subfinder ... ${RESET}"
subfinder -d "$domain" > "$subdomain_path/found.txt"

echo -e "${RED} [+] Running assetfinder ... ${RESET}"
assetfinder "$domain" | grep "$domain" >> "$subdomain_path/found.txt"

echo -e "${RED} [+] Checking what's alive ... ${RESET}"
cat "$subdomain_path/found.txt" | grep "$domain" | sort -u | httprobe | grep https | sed 's/https\?:\/\///' | tee -a "$subdomain_path/alive.txt"

echo -e "${RED} [+] Taking screenshots ... ${RESET}"
gowitness scan file -f "$subdomain_path/alive.txt" --screenshot-path $screenshot_path/ --no-http
```