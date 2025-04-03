# Docker Express React Packager

Hello there👋🏼👋🏼👋🏼  

A few days ago, I wanted to revisit Docker, that magical tool that lets us package our projects and make it incredibly easy to share them without requiring others to install countless dependencies or waste time configuring environments. The thing is that dev packages size are to large and sometimes require a lot of settings, so I thougth about developing a simple express server that allows me to package built react projects.

I mention only React because how it works, with a static page that render the content by the path and conditionals, so the server settings are easy for the basic knowledge I currently have.

### Technologies Used  
- **Express**: A Node.js framework used to set up the server. It’s all I need for this project.

## You want to try it?

This is essentially a tool, so the best way to use it is by cloning the repository. You’ll also need:  
- **Node.js** (to run the server)  
- **Docker** (only if you plan to dockerize your project)  

You'll need to provide a built react project in a "dist" folder in the root from the directory. More information about this below...

If you need to pack a project scroll to the bottom of this README.

### Get the repository to your local workspace

First of all you need to clone the repository:
```bash
$ git clone https://github.com/fgamester/docker-express-react-packager.git
```
Then run the packages installation command:
```bash
$ npm install
```
Finally, run the server locally with the following command:
```bash
$ node server.js
```
The project will be running at the 3000 port, so you will have to type [`localhost:3000`](http://localhost:3000) on your web browser or go there by clicking the marked link.

### What's next?

As mentioned earlier, you’ll need a built React project inside a `dist` folder.  

First of all create a `dist` folder at the root directory of the project or copy it from your own project, but ensure that is in the main directory. Something like this:

```
this-project/  
├── dist/  
│   ├── index.html
│   ├── styles.css
│   ├── app.js
│   └── assets/
│       ├── image.png
│       └── logo.png
├── node_modules/
├── .dockerignore
├── .gitignore
├── Dockerfile
├── package-lock.json
├── package.json
├── README.md
└── server.js
```

Your actual files may vary, just make sure they stay inside `dist`.

Aditionaly in server.js file you can change the expose port in case you could need it, it is set to port 3000 by default. if you change it REMEMBER to change it in Dockerfile too (just in case you wanted to pack it).

`server.js`:
```javascript
const express = require('express');
const path = require('path');
const app = express();
const port = 3000; // <--- CHANGE IT HERE

app.use(express.static(path.join(__dirname, 'dist')));

app.get('*', (req, res) => {
    res.sendFile(path.join(__dirname, 'dist', 'index.html'));
});

app.listen(port, () => {
    console.log(`Server is running at port ${port}`);
});
```
`Dockerfile`:
```dockerfile
FROM node:16-alpine
WORKDIR /app
COPY . .
RUN npm install
EXPOSE 3000 # <--- CHANGE IT HERE
CMD ["node","server.js"]
```

### Dockerize

If you need a packed project to show to your clients, co-workers, students, friends, etc., here is how you can do it.

I´ll assume you already install Docker, now you have to open a terminal that points to the root directory of the project,
there you need to use the following command (in case you don't know much about Docker, I recommend you to read the command's description):
```bash
$ docker build -t custom-name:custom-tag .
```
`-t`: it means `--tag`, allows us to write a name and optionally a tag for the Docker image that we´re building.  
`custom-name`: if you use `-t` you'll have to write a name for your image, use one that could be easily remembered.  
`custom-tag`: this one is optional, the tag of a Docker image is usually used to differentiate its versions, if you're sure this will be the only version of your image, you can skip it, just make sure to delete the `:` symbol after the image name.  
`.`: this dot is used to specify where the Dockerfile is, relative to the current terminal path.

After this the terminal will show some details about the process, just wait until it finishes.

#### Use your image

Now everything is ready to set up a container with our image. Open a new terminal, it doesn't matter which path you're in, now we'll work directly with Docker CLI.

Run the following command:
```bash
$ docker run --name custom-name -p 3000:3000 -d image-name
```
`--name`: Allows us to specify a `custom-name` to our container, make sure it is one you can remember, but it is essentially optional.  
`-p`: This is required, as you need to specify a port that allows us to communicate with the container.
`3000:3000`: The first one is which port we will be able to use to connect from OUTSIDE of the container, this one is relatively optional (in wich number to use), just make sure it is a available (not used by others applications). The second one is the one we setted up before we built the image, if you didn't change anything then it is the port `3000`.

After that and if we didn't have any issues, the terminal will respond with a generated container ID. Additionally the `run` command will immediatly start our container.

And that's it, now you can visit localhost:3000 or whatever port you set up before to see your built react page. 

Whenever you want to turn it off, you just use the command `stop`.
```bash
$ docker stop container-name
```
You can use the name or the ID, I recommend using the name if you set one.