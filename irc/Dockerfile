FROM buildpack-deps:jammy

# create an unpriviledged user to make and run irc in
RUN useradd ircd -m --password ircd
# switch to the new user
USER ircd
# switch to the user's working directory
WORKDIR /home/ircd

# fetch unreal sources
RUN wget --trust-server-names https://www.unrealircd.org/downloads/unrealircd-6.0.7.tar.gz
#unpack
RUN tar xzvf unrealircd-6.0.7.tar.gz
# switch to our new working directory
WORKDIR /home/ircd/unrealircd-6.0.7

# run default configuration
RUN ./Config
# build unreal
RUN make -j 2 install

# change to unreal's install directory directory
WORKDIR ../unrealircd

WORKDIR conf/ssl

RUN openssl req -new -sha256 -newkey rsa:4096 -keyout server.key.pem -out server.req.pem -subj "/C=US/ST=OH/O=Pydle/CN=irc.hullseals.dev" -nodes && openssl req -x509 -key server.key.pem -in server.req.pem -weeks 1 -out server.cert.pem
WORKDIR ..
COPY ./ircd.conf unrealircd.conf
WORKDIR ..

ENTRYPOINT ./unrealircd start; tail -f logs/ircd.log