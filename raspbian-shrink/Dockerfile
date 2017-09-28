FROM ubuntu:latest

RUN apt-get -y update && apt-get -y install dcfldd

# Copy the raspbian-shrink script file into somewhere on the $PATH
COPY ./cotswoldjam/raspbian-shrink /usr/local/bin

# Change the script permissions
RUN chmod a+x /usr/local/bin/raspbian-shrink

WORKDIR /work-dir

VOLUME [ "/work-dir" ]

ENTRYPOINT [ "/usr/local/bin/raspbian-shrink" ]
CMD ["original.img", "shrunk.img"]
