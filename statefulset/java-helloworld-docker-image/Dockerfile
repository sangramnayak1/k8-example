FROM --platform=linux/amd64 openjdk:8

WORKDIR /app

COPY . /app/

RUN javac Main.java

ENTRYPOINT [ "/bin/bash", "-c", "java Main" ]
