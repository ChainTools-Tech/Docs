# Install Yarn

## Yarn Package Manager installation

This set of command is noted from Node.js installation.\
It comes up as suggestion once node.js is isntalled.\
Just wrote this down to have it handy for deployments.

### To install the Yarn package manager, run: <a href="#user-content-to-install-the-yarn-package-manager-run" id="user-content-to-install-the-yarn-package-manager-run"></a>

```
curl -sL https://dl.yarnpkg.com/debian/pubkey.gpg | gpg --dearmor | sudo tee /usr/share/keyrings/yarnkey.gpg >/dev/null
echo "deb [signed-by=/usr/share/keyrings/yarnkey.gpg] https://dl.yarnpkg.com/debian stable main" | sudo tee /etc/apt/sources.list.d/yarn.list
sudo apt-get update && sudo apt-get install yarn
```
