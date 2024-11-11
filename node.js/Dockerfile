# Pull image from Redhat
FROM registry.access.redhat.com/ubi9/nodejs-20:1-54.1724037697

# Change user to root
USER root

# Update all packages
RUN yum update -y

# Remove unneeded packages
# Install packages you need with `RUN yum install -y PACKAGE_NAME`
RUN yum remove -y python-setuptools python-urllib3 python-requests python-idna 

# Sets the working directory for the following RUN, CMD, ENTRYPOINT, COPY and ADD commands
WORKDIR /app

# Copy files from your local machine to the docker image
COPY package.json package-lock.json ./

COPY . .

# Run ci (ci = clean install) to install all libraries as in package.json
RUN npm ci

# Uninstall unneeded libraries
RUN npm uninstall braces

# Build the js app
RUN npm run build

# Switch to non-admin user (depends on image used, here in Redhat, the non-admin user is called 1001)
USER 1001

# Expose the port that would be serving the js app
EXPOSE 8080

# Command to start the js app
CMD ["npm", "start", "--", "--port", "8080"]