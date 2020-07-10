FROM nginx:latest

COPY init /usr/local/bin/init

# overwrite the entrypoint of base image
ENTRYPOINT ["/usr/bin/env"]

# run init script to replace log path
CMD ["init"]
