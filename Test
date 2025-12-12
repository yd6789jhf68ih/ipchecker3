#!/usr/bin/env python3
"""
Username Availability Checker
Checks username availability across popular platforms
"""

import requests
import json
import time
import concurrent.futures
from colorama import init, Fore, Style
from datetime import datetime

# Initialize colorama
init(autoreset=True)

class UsernameChecker:
    def __init__(self):
        self.platforms = {
            'GitHub': {
                'url': 'https://github.com/{}',
                'check_method': 'status_code',
                'available_if': 404,
                'unavailable_if': 200
            },
            'Twitter': {
                'url': 'https://twitter.com/{}',
                'check_method': 'status_code',
                'available_if': 404,
                'unavailable_if': 200
            },
            'Instagram': {
                'url': 'https://www.instagram.com/{}/',
                'check_method': 'status_code',
                'available_if': 404,
                'unavailable_if': 200
            },
            'Reddit': {
                'url': 'https://www.reddit.com/user/{}',
                'check_method': 'status_code',
                'available_if': 404,
                'unavailable_if': 200
            },
            'Pinterest': {
                'url': 'https://www.pinterest.com/{}/',
                'check_method': 'status_code',
                'available_if': 404,
                'unavailable_if': 200
            },
            'TikTok': {
                'url': 'https://www.tiktok.com/@{}',
                'check_method': 'status_code',
                'available_if': 404,
                'unavailable_if': 200
            },
            'Steam': {
                'url': 'https://steamcommunity.com/id/{}',
                'check_method': 'status_code',
                'available_if': 404,
                'unavailable_if': 200
            },
            'SoundCloud': {
                'url': 'https://soundcloud.com/{}',
                'check_method': 'status_code',
                'available_if': 404,
                'unavailable_if': 200
            },
            'Spotify': {
                'url': 'https://open.spotify.com/user/{}',
                'check_method': 'status_code',
                'available_if': 404,
                'unavailable_if': 200
            },
            'Twitch': {
                'url': 'https://www.twitch.tv/{}',
                'check_method': 'status_code',
                'available_if': 404,
                'unavailable_if': 200
            },
            'Telegram': {
                'url': 'https://t.me/{}',
                'check_method': 'content',
                'available_if': 'tgme_page_title" content="Telegram: Contact',
                'unavailable_if': 'tgme_page_title" content="@'
            },
            'YouTube': {
                'url': 'https://www.youtube.com/@{}',
                'check_method': 'status_code',
                'available_if': 404,
                'unavailable_if': 200
            },
            'Medium': {
                'url': 'https://medium.com/@{}',
                'check_method': 'status_code',
                'available_if': 404,
                'unavailable_if': 200
            },
            'Dev.to': {
                'url': 'https://dev.to/{}',
                'check_method': 'status_code',
                'available_if': 404,
                'unavailable_if': 200
            },
            'Product Hunt': {
                'url': 'https://www.producthunt.com/@{}',
                'check_method': 'status_code',
                'available_if': 404,
                'unavailable_if': 200
            }
        }
        
        self.session = requests.Session()
        self.session.headers.update({
            'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36'
        })
        
    def check_platform(self, platform_name, platform_info, username):
        """Check username availability on a specific platform"""
        try:
            url = platform_info['url'].format(username)
            
            # Add delay to avoid rate limiting
            time.sleep(0.5)
            
            response = self.session.get(url, timeout=10, allow_redirects=True)
            
            if platform_info['check_method'] == 'status_code':
                if response.status_code == platform_info['available_if']:
                    return platform_name, True, url  # Available
                elif response.status_code == platform_info['unavailable_if']:
                    return platform_name, False, url  # Taken
                else:
                    return platform_name, None, url  # Unknown
            
            elif platform_info['check_method'] == 'content':
                content = response.text
                if platform_info['available_if'] in content:
                    return platform_name, True, url
                elif platform_info['unavailable_if'] in content:
                    return platform_name, False, url
                else:
                    return platform_name, None, url
                    
        except requests.exceptions.Timeout:
            return platform_name, None, f"Timeout checking {platform_name}"
        except requests.exceptions.ConnectionError:
            return platform_name, None, f"Connection error for {platform_name}"
        except Exception as e:
            return platform_name, None, f"Error: {str(e)}"
        
        return platform_name, None, url
    
    def check_username(self, username):
        """Check username availability across all platforms"""
        print(f"\n{Fore.CYAN}🔍 Checking username: @{username}")
        print(f"{Fore.CYAN}{'='*60}")
        
        results = {
            'username': username,
            'timestamp': datetime.now().isoformat(),
            'available': [],
            'taken': [],
            'unknown': []
        }
        
        # Use threading for faster checking
        with concurrent.futures.ThreadPoolExecutor(max_workers=5) as executor:
            futures = []
            for platform_name, platform_info in self.platforms.items():
                future = executor.submit(
                    self.check_platform, 
                    platform_name, 
                    platform_info, 
                    username
                )
                futures.append(future)
            
            for future in concurrent.futures.as_completed(futures):
                platform, available, url = future.result()
                
                if available is True:
                    results['available'].append(platform)
                    print(f"{Fore.GREEN}✅ {platform}: Available")
                elif available is False:
                    results['taken'].append(platform)
                    print(f"{Fore.RED}❌ {platform}: Taken - {url}")
                else:
                    results['unknown'].append(platform)
                    print(f"{Fore.YELLOW}⚠️  {platform}: Could not determine - {url}")
        
        return results
    
    def generate_report(self, results):
        """Generate a detailed report"""
        print(f"\n{Fore.CYAN}{'='*60}")
        print(f"{Fore.CYAN}📊 CHECK RESULTS SUMMARY")
        print(f"{Fore.CYAN}{'='*60}")
        
        username = results['username']
        
        print(f"\n{Fore.WHITE}👤 Username: @{username}")
        print(f"{Fore.WHITE}📅 Checked: {datetime.fromisoformat(results['timestamp']).strftime('%Y-%m-%d %H:%M:%S')}")
        
        print(f"\n{Fore.GREEN}✅ AVAILABLE ({len(results['available'])} platforms):")
        for platform in sorted(results['available']):
            print(f"   • {platform}")
        
        print(f"\n{Fore.RED}❌ TAKEN ({len(results['taken'])} platforms):")
        for platform in sorted(results['taken']):
            print(f"   • {platform}")
        
        if results['unknown']:
            print(f"\n{Fore.YELLOW}⚠️  UNKNOWN ({len(results['unknown'])} platforms):")
            for platform in sorted(results['unknown']):
                print(f"   • {platform}")
        
        # Recommendations
        print(f"\n{Fore.CYAN}💡 RECOMMENDATIONS:")
        print(f"{Fore.WHITE}1. If taken on many platforms, consider different username")
        print(f"{Fore.WHITE}2. Register available platforms quickly")
        print(f"{Fore.WHITE}3. Use consistent username across platforms")
        print(f"{Fore.WHITE}4. Consider variations if original is taken")
        
        return results
    
    def save_results(self, results, filename=None):
        """Save results to JSON file"""
        if filename is None:
            timestamp = datetime.now().strftime("%Y%m%d_%H%M%S")
            filename = f"username_check_{results['username']}_{timestamp}.json"
        
        try:
            with open(filename, 'w', encoding='utf-8') as f:
                json.dump(results, f, indent=2, ensure_ascii=False)
            print(f"\n{Fore.GREEN}💾 Results saved to: {filename}")
            return filename
        except Exception as e:
            print(f"{Fore.RED}❌ Error saving results: {e}")
            return None
    
    def suggest_alternatives(self, username):
        """Suggest alternative usernames"""
        print(f"\n{Fore.CYAN}💡 SUGGESTED ALTERNATIVES:")
        print(f"{Fore.CYAN}{'-'*40}")
        
        suggestions = []
        
        # Add numbers
        for i in ['', '1', '2', '3', '2024', '99', 'official']:
            suggestions.append(f"{username}{i}")
        
        # Add prefixes/suffixes
        prefixes = ['real', 'the', 'its', 'im', 'weare']
        suffixes = ['world', 'global', 'hq', 'official', 'team']
        
        for prefix in prefixes[:2]:
            suggestions.append(f"{prefix}{username}")
        
        for suffix in suffixes[:2]:
            suggestions.append(f"{username}{suffix}")
        
        # Add underscore
        suggestions.append(f"{username}_")
        suggestions.append(f"_{username}")
        
        # Show suggestions
        for i, suggestion in enumerate(suggestions[:10], 1):
            print(f"{i}. @{suggestion}")
        
        return suggestions
    
    def quick_check(self, username):
        """Quick check for most important platforms"""
        important_platforms = ['GitHub', 'Twitter', 'Instagram', 'YouTube', 'Telegram']
        
        print(f"\n{Fore.CYAN}⚡ Quick Check for: @{username}")
        
        quick_results = {'available': [], 'taken': []}
        
        for platform in important_platforms:
            if platform in self.platforms:
                platform_name, available, url = self.check_platform(
                    platform, 
                    self.platforms[platform], 
                    username
                )
                
                if available is True:
                    quick_results['available'].append(platform)
                    print(f"{Fore.GREEN}✅ {platform}: Available")
                elif available is False:
                    quick_results['taken'].append(platform)
                    print(f"{Fore.RED}❌ {platform}: Taken")
                else:
                    print(f"{Fore.YELLOW}⚠️  {platform}: Unknown")
        
        return quick_results
    
    def interactive_mode(self):
        """Interactive user interface"""
        print(f"\n{Fore.CYAN}{'='*60}")
        print(f"{Fore.CYAN}👤 USERNAME AVAILABILITY CHECKER")
        print(f"{Fore.CYAN}{'='*60}")
        
        print(f"\n{Fore.YELLOW}⚠️  Note: This tool checks PUBLIC availability only")
        print(f"{Fore.YELLOW}   It does NOT create accounts or reserve usernames")
        
        username = input(f"\n{Fore.WHITE}Enter username to check (without @): ").strip()
        
        if not username:
            print(f"{Fore.RED}❌ No username entered!")
            return
        
        # Validate username
        if len(username) < 3:
            print(f"{Fore.RED}❌ Username too short (min 3 characters)")
            return
        
        if len(username) > 30:
            print(f"{Fore.RED}❌ Username too long (max 30 characters)")
            return
        
        # Check mode
        print(f"\n{Fore.WHITE}Check mode:")
        print(f"{Fore.CYAN}1. Quick check (5 major platforms)")
        print(f"{Fore.CYAN}2. Full check (15+ platforms)")
        
        mode = input(f"\n{Fore.WHITE}Select mode (1/2): ").strip()
        
        print(f"\n{Fore.YELLOW}🔄 Checking... This may take a moment...")
        
        if mode == '1':
            results = self.quick_check(username)
        else:
            results = self.check_username(username)
            results = self.generate_report(results)
            
            # Save results
            save = input(f"\n{Fore.WHITE}Save results to file? (y/n): ").lower()
            if save == 'y':
                self.save_results(results)
        
        # Suggest alternatives if many taken
        if 'taken' in results and len(results['taken']) > 5:
            show_alts = input(f"\n{Fore.WHITE}Show alternative suggestions? (y/n): ").lower()
            if show_alts == 'y':
                self.suggest_alternatives(username)
        
        print(f"\n{Fore.GREEN}{'='*60}")
        print(f"{Fore.GREEN}✅ Check complete!")
        print(f"{Fore.GREEN}{'='*60}")

def main():
    """Main function"""
    try:
        checker = UsernameChecker()
        checker.interactive_mode()
        
        # Ask to check another
        while True:
            another = input(f"\n{Fore.WHITE}Check another username? (y/n): ").lower()
            if another != 'y':
                break
            checker.interactive_mode()
        
        print(f"\n{Fore.CYAN}👋 Goodbye! Happy username hunting!")
        
    except KeyboardInterrupt:
        print(f"\n{Fore.RED}\n⚠️  Operation cancelled")
    except Exception as e:
        print(f"{Fore.RED}❌ Error: {e}")

if __name__ == "__main__":
    main()
