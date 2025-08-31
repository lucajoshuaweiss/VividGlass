# VividGlass
![](VividGlass.png?raw=true)  
VividGlass is a minimalist music player featuring a glassmorphism design.

# Usage
Install the dependencies:
```bash
npm install
```

Build the app:
```bash
npm run build
```

You will find the app under "dist".

Tested with Mozilla Firefox 142.0.1.

# Docker
You can also run the app as a docker image:


Build the image:
```bash
docker build -t vividglass .
```

Run the container, mapping the desired port:
```bash
docker run -p <dockerPort>:8080 vividglass
```
