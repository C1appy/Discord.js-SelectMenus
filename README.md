To get started make a new file in your handler file or wherever you wish this to be.

**Paste this code below into said file**

```js
const { resolve } = require('path');
const { readdir } = require('fs').promises;
const chalk = require('chalk');
module.exports = (client) => {
  
  async function getFiles(dir) {
    const dirents = await readdir(dir, { withFileTypes: true });
    const files = await Promise.all(dirents.map((dirent) => {
      const res = resolve(dir, dirent.name);
      return dirent.isDirectory() ? getFiles(res) : res;
    }));
    return Array.prototype.concat(...files);
  }
  
  async function run(){
      const files = await getFiles("./SelectMenus/");
      const jsFiles = files.filter((f) => f.split(`.`).pop() === "js")
      if (jsFiles.length <= 0)
      return console.log(chalk.yellowBright.bold(`[Select-Menu-Handler] No loadable Menus detected`));
          //console.log(`[SelectMenus]: Loaded ${jsFiles.length} SelectMenus`);
          console.log(chalk.magentaBright.bold(`[Select-Menu-Handler]: Loaded ${jsFiles.length} SelectMenus`))
      // console.log(`[${`Loader`}] Loading ${jsFiles.length} Select Menus`);
      jsFiles.forEach((fileName, index) => {
          let props = require(fileName);
          // console.log(`[${`Loader`}] ${fileName} Select Menus loaded (${index + 1})`)
          client.interactions.set(props.help.values, props);
      });
  }
  run();

  client.on(`interactionCreate`, async (interaction) => {
    if (interaction.isSelectMenu()) {
      var cmd = client.interactions.get(interaction.values[0]);
      if (cmd) cmd.run(client, interaction);
    }
  });
};
```

After this is done go to your main file (normally index.js) and paste this somewhere

`require(./Handlers/SelectMenus)(client);`

**Make sure to change** `./Handlers/SelectMenus` **to the path where you have it**


Once done make a new folder in your main directory named `SelectMenus` and then add all your Select Menu Values in there they can be in folders for keeping stuff neat.

**To use this**

```js

module.exports.run = async (client, interaction) => {

            //the code to be executed goes here

}
module.exports.help = {
    values: `the value of your menu goes here`,
};
```
