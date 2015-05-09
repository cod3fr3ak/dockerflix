Dockerflix
========

Want to watch U.S. Netflix, Hulu, MTV, Vevo, Crackle, ABC, NBC, PBS...?  
Got a Dnsmasq capable router at home, a Raspberry Pi or similar Linux computer?  
Got a virtual private server with a U.S. IP address?  
Know how to set up the latest and greatest version of Docker?  
**Then you've come to the right place!**

Simply said, Dockerflix emulates what companies like Unblock-Us and the like have been doing for years. Dockerflix uses a man-in-the-middle approach to reroute certain requests through a (your) server in the U.S. and thus tricks geo-fenced on-demand streaming media providers into thinking the request originated from within the U.S. This so-called DNS unblocking approach differs vastly from a VPN.

Since my [other  DNS unblocking project](https://github.com/trick77/tunlr-style-dns-unblocking) wasn't easy to install and hard to maintain, I came up with a new variant using [dlundquist's](https://github.com/dlundquist) [sniproxy](https://github.com/dlundquist/sniproxy) instead of HAproxy. To make the installation a breeze, I boxed the proxy into a Docker container and wrote a small, Python-based Dnsmasq configuration generator.

## Installation

Clone this Github repository on your VPS server and build the Dockerflix image using the provided shell script:  
 `./build.sh`

## Usage

Once the Dockerflix image has been built, just run it using:  
`docker run -p 80:80 -p 443:443 --name dockerflix trick77/dockerflix`

Make sure TCP ports 80 and 443 on your VPS are not in use by some other software like a pre-installed web server. Check with `netstat -tulpn` when in doubt. Make sure both ports are accessible from the outside if using an inbound firewall on the VPS server.

From now on, the Dockerflix container can be resumed or suspended using `docker start dockerflix` and `docker stop dockerflix`

To see if the Dockerflix container is up and running use `docker ps` or `docker ps -a`. Want to get rid of Dockerflix? Just type `docker stop dockerflix; docker rm dockerflix` and it's gone. 

## Post installation

Now that we have set up the proxy, we need to make sure only the **relevant** DNS queries get answered with the VPS' public IP address. Generate a Dnsmasq configuration using:  
`python ./gendns-conf.py -r <PUBLIC_IP_OF_YOUR_VPS_SERVER>`

This configuration has to be used in your home router (if it runs Dnsmasq for DNS resolution) or a Linux-based computer like the Raspberry Pi. Obviously, all DNS requests originating at home have to be resolved/forwarded through Dnsmasq from now on.

## Test

Everything has been set up properly once your VPS' IP address shows up in the web browser when navigating to http://ipinfo.io/

If the web browser shows your home IP there's something wrong with DNS resolution. Make sure not to fall into the browser DNS caching trap, always restart after changing DNS addresses.

## Limitations

Dockerflix only handles requests using plain HTTP or TLS using the SNI extension. Some multimedia players don't support SNI and thus won't work with Dockerflix. 

## Supported on-demand Internet streaming services 

| Service                                                         | Web browsers    | iOS | Android |
| --------------------------------------------------------------- | --------------- | --- | ------- |
| [Netflix](https://www.netflix.com/us/)                          | Yes             | Yes |         |
| [Hulu](http://www.hulu.com/) (see below)                        | Yes             | Yes |         |
| [MTV](http://www.mtv.com/videos/home.jhtml)                     | Yes             |     |         |
| [Vevo](http://www.vevo.com/)                                    | Yes             |     |         |
| [Crackle](http://www.crackle.com/)                              | Yes             |     |         |
| [ABC](http://abc.go.com/)                                       | Yes             |     |         |
| [NBC](http://www.nbc.com/video/full-episodes)                   | Yes             |     |         |
| [PBS](http://video.pbs.org/)                                    | Yes             |     |         |
| [LogoTV](http://www.logotv.com/video/showall.jhtml)             | Yes             |     |         |
| [CWTV](http://www.cwtv.com/shows/)                              | Yes             |     |         |
| [Southpark](http://southpark.cc.com/)                           | Yes             |     |         |
| [Smithsonian](http://www.smithsonianchannel.com/full-episodes)  | Yes             |     |         |
| [Spike](http://www.spike.com/episodes)                          | Yes             |     |         |
| [Cooking Channel TV](http://www.cookingchanneltv.com/)          | Yes             |     |         |
| [Pandora](http://www.pandora.com/)                              | Yes             | Yes |         |
| [iHeart Radio](http://www.iheart.com/)                          | Yes             |     |         |

Hulu has blacklisted many VPS providers in the U.S. You have to be lucky to find one which still works.

## Contributing

Please contribute using pull requests instead of opening issues to complain that this or that doesn't work. No one gets paid here, so don't expect any support.


