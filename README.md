# Discord Friends List Extractor

A simple browser script to export your Discord friends list including display names, handles, and user IDs.

## 🚀 Usage

1. Open Discord in your web browser (discord.com/app) - **NOT** the desktop app
2. Go to your Friends list
3. Click the "All" filter at the top
4. Open Developer Tools:
   - Press F12
   - Or right-click and select "Inspect"
5. Click on the "Console" tab in Developer Tools
6. Copy and paste the script below
7. Press Enter to run it
8. A small control panel will appear in the top-right corner
9. Slowly scroll through your entire friends list
10. Click "Save List" to download your friends list
11. Click "Stop" when you're done

## 📝 Output Format

The script generates a text file containing your friends' information in this format:
```
DisplayName (Handle: actual.username) (ID: 123456789)
```
Where:
- DisplayName: The name shown in Discord
- Handle: Their unique username for adding as friend
- ID: Their Discord user ID

## ⚙️ The Script

```javascript
(() => {
    const friendsList = new Set();
    const seenIds = new Set();
    let isRunning = true;
    
    function collectFriends() {
        const friendElements = document.querySelectorAll('.userInfo_f3939d');
        let newFound = 0;
        
        friendElements.forEach(element => {
            try {
                // Get display name
                const usernameElement = element.querySelector('.username_f3939d');
                // Get their handle/discriminator
                const discriminatorElement = element.querySelector('.discriminator_f3939d');
                
                if (!usernameElement || !discriminatorElement) return;
                
                const displayName = usernameElement.textContent.trim();
                const handle = discriminatorElement.textContent.trim();
                const avatarImg = element.querySelector('img[class*="avatar"]');
                
                if (avatarImg?.src) {
                    const match = avatarImg.src.match(/avatars\/(\d+)/);
                    if (match && !seenIds.has(match[1])) {
                        seenIds.add(match[1]);
                        friendsList.add(`${displayName} (Handle: ${handle}) (ID: ${match[1]})`);
                        newFound++;
                    }
                }
            } catch (e) {
                console.error('Error processing friend element:', e);
            }
        });
        
        if (newFound > 0) {
            console.log(`Found ${newFound} new friends. Total unique friends: ${friendsList.size}`);
        }
        
        if (isRunning) {
            requestAnimationFrame(collectFriends);
        }
    }
    
    // Start collecting
    console.log("Starting friend collection. Please scroll through your friends list...");
    collectFriends();
    
    // Create a floating status display
    const status = document.createElement('div');
    status.style.cssText = `
        position: fixed;
        top: 10px;
        right: 10px;
        background: #202225;
        color: white;
        padding: 10px;
        border-radius: 5px;
        z-index: 9999;
        font-family: Arial, sans-serif;
        box-shadow: 0 2px 10px rgba(0,0,0,0.2);
    `;
    
    // Add buttons
    const buttonStyle = `
        margin: 5px;
        padding: 5px 10px;
        border: none;
        border-radius: 3px;
        cursor: pointer;
        background: #5865F2;
        color: white;
    `;
    
    status.innerHTML = `
        <div id="friendCount">Friends found: 0</div>
        <button id="saveFriends" style="${buttonStyle}">Save List</button>
        <button id="stopCollection" style="${buttonStyle}">Stop</button>
    `;
    
    document.body.appendChild(status);
    
    // Update counter
    setInterval(() => {
        document.getElementById('friendCount').textContent = `Friends found: ${friendsList.size}`;
    }, 1000);
    
    // Save button handler
    document.getElementById('saveFriends').onclick = () => {
        const sortedFriends = Array.from(friendsList).sort();
        const formattedList = sortedFriends.join('\n');
        
        const blob = new Blob([formattedList], { type: 'text/plain' });
        const url = window.URL.createObjectURL(blob);
        const a = document.createElement('a');
        a.href = url;
        a.download = 'discord_friends.txt';
        document.body.appendChild(a);
        a.click();
        document.body.removeChild(a);
        window.URL.revokeObjectURL(url);
        
        console.log("Friends list saved!");
        console.log("Full list:");
        console.log(formattedList);
    };
    
    // Stop button handler
    document.getElementById('stopCollection').onclick = () => {
        isRunning = false;
        status.remove();
        console.log("Collection stopped. Final count:", friendsList.size);
    };
    
    return "Friend collector started. Please scroll through your friends list. A control panel has been added to the top-right corner.";
})();
```

## ⚠️ Important Notes

- This script only works on Discord's website, not the desktop app
- You must manually scroll through your friends list
- The script only collects information that is visible on your screen
- This is an unofficial tool and not affiliated with Discord
- Discord's interface may change, which could break this script
- The script runs entirely in your browser and doesn't send data anywhere

## 🔒 Privacy & Safety

This script:
- Only collects publicly visible information from your friends list
- Runs locally in your browser
- Doesn't send any data to external servers
- Doesn't modify any Discord functionality
- Doesn't access any private information

## 🤔 Common Issues

1. **Script not working?**
   - Make sure you're on discord.com/app, not the desktop app
   - Ensure you're on the Friends tab with "All" selected
   - Try refreshing the page and running the script again

2. **Not all friends showing up?**
   - Scroll through your entire friends list slowly
   - Make sure the counter in the top-right stops increasing before saving

## 📄 License

MIT License - feel free to modify and share!

## 🤝 Contributing

Found a bug or want to improve the script? Feel free to:
1. Open an issue
2. Submit a pull request
3. Fork the repository

## 📚 Version History

- v1.0.0 (2024-12-18): Initial release

## ❓ Need Help?

Open an issue in this repository if you need assistance or find any bugs.

## ⭐ Support

If you find this tool helpful, please consider giving it a star! It helps others discover this resource.
