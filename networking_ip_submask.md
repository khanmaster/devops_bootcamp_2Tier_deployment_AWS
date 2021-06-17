
# Networking Intro

## This lesson includes

* Networking
* IP Networks
* IP Addresses
* IPs and Binary
* Subnets
* CIDR blocks
* Routing
* Network Address Translation

Let's keep this simple. A network is what we get when we connect two or more devices, usually machines or other network devices, together.

In our Vagrant environment we have two virtual instances that are able to talk to eachother. This is because VirtualBox has handled networking them together.

You will all almost certainly have a network at home. All of your home laptops and phones are connected via WIFI or cables to a router. Whether they can communicate with eachother is determined by the network is setup. A home router is mostly used to allow devices on the network to connect the internet. But that doesn't have to be all it can do.

Our simple network of two virtual machines ( and the host for that matter ) works perfectly well but it has absolutely no security. Everything can talk to everything. We're going to look at how to create a network ourselves in a VPC and make it more secure and fit for purpose.

But before we can do that we need to learn some fundamentals of IP networking.

## IP Networks

By far the most commonly used type of network is an IP network. IP stands for Internet Protocol and it's used everywhere.

At it's core an IP is a physical location of a machine that can "speak" IP. Let's use an analogy to explain this.

Imagine you're stating in a hotel. For our purposes each room is going to represent something like a computer or a phone or anything else that can be connected to a network.

What we want is to be able to send messages between the rooms. So how could we do that in the hotel? Let's assume that you're staying in room 204 on the second floor and you want to deliver a message to room 203. 

It should be pretty clear that you can just walk along the corridor to room 204 and pop the message under the door. Why? Because the rooms are connected by the corridor.

This is a very simple example of a network. We could easily do this with computers too. We could connect two computers directly together with an ethernet cable so they could communicate but what is more common is that we would connect multiple machines together using something called a switch. This would allow all the machines to talk to all the other machines just like all the rooms connected by the corridor can send messages to each other.

That's a very simple network. It's the sort of thing you might find at your office or at home connecting all your machines together.

## IP Addresses

So in our hotel example we have room numbers to identify each room but what do room numbers look like in an IP network. Well you will have definitely seen then before. We identify each machine via an IP address. Something like:

```
192.168.10.100
```

> EXERCISE: Open up the network preferences on your mac and find you're current IP address.

You'll see that all your machines have a unique IP address. None of you should be sharing an IP address and if you are then you're WIFI probably isn't working.

So if one machine wants to send a message to another it can use it's IP address to say where it's going.

Let's examine how IP addresses work in a little more detail because it will help us to understand a few things later on.

## IPs and Binary

IPs are made of four sets of numbers that run from 0 to 255 called octets ( we'll explain why in a second ).

The smallest IP you can have is:

```
0.0.0.0
```

And the highest is:

```
255.255.255.255
```

All IPs in between are valid too.

> QUESTION: How many different IP addresses can there be?

If you're maths is good you should be able to see that:

```
256 * 256 * 256 * 256 = 4,294,967,296
```

Or roughly 4.3 Billion different IP addresses. That's quite a few! Remember it's 256 not 255 because you include the 0!

This type of IP address is called IPv4 or IP version 4.

> QUESTION: How many people live on the earth?

The answer at the time of writing is roughly 7.4 billion people. Now not everyone on the planet has a computer but a lot of people do! And in modern society most people have well over five networkable devices like phones, tablets, laptops, TVs, fridges you name it.

So we are definitely getting close to the limit of our IP addresses! If every single device got given an IP address we'd have run out of IP addresses long ago.

Luckily there are two solutions to this; subnetting and IPv6.

IPv6 allows bigger numbers to be used and as such more IP addresses but it's actually not that widely used yet. That is because subnetting solved the problem very effectively!

## Subnets

A subnet it simply a sub network. A smaller network of a few machines inside a larger network. In our hotel analogy each corridor would be a subnet. 

These machines are connected directly and can talk to each other directly. In the real world we would typically put machines together in subnet for business reasons. We might put all the accountancy machines in one subnet and all the developer machines in another so that we can control who can see what.  

Let's go back to our original example of sending a message from room 204 to 203. Think about what steps you would take in real life.

One of your first ( probably unconscious ) thoughts would be "is room 203 on my floor?". Well, if the hotel used a fairly standard floor/room numbering system you could guess that all the rooms on the second floor start with a 2. 

From that system it's pretty easy to tell that room 203 is on your floor and room 105 is on the first floor i.e in a different subnet. 

This is exactly how subnets work in IP networks too. Except that we get to choose our own numbering system to label each subnet ( floor ).

Let's look at an IP again:

```
192.168.10.100
``` 

How much of this IP address tells us which subnet it's in? The answer is we don't know. We get to choose that when we design our network. There are three ways to define this. Let's take a look.

### Class Networks

Class networks are pretty old now and aren't really used but the terminology is still used so it's worthwhile knowing about them.

A "Class A" network is simply one where the first part of the IP ( the first octet ) identifies the subnet, the floor number if you like.

In a class a network it would be easy to see the follwing:

```
192.168.100.10 # Same subnet
192.169.50.0 # Same subnet
193.170.50.10 # Different subnet
```

From here you go up in octets to Class B where the first two octets identify the subnet ( floor )

In a class B network none of those three would be the same:

```
192.168.100.10 # Different subnet
192.169.50.0 # Different subnet
193.170.50.10 # Different subnet
```

And Class C is the first three octets. Simple as that.

### Subnet Mask

The subnet mask is far more commonly used to label our subnets. But to understand it we need to learn some binary. But we're finally going to learn why we call the parts of an IP octets.

The reason our IP parts only go up to 255 is because they are actually stored as 8-bit binary numbers. So we're going to need a refresher on binary.

Binary is a very powerful way of storing numbers using only 0s and 1s. We use the 0s and 1s as "switches" called bits. When we turn on a switch it adds the value of that switch to our total. Let's look at an example:

| 128 | 64 | 32 | 16 | 8 | 4 | 2 | 1 |
|:---:|:--:|:--:|----|---|---|---|---|
|  0  |  0 |  0 |  0 | 0 | 0 | 0 | 0 |

The top row here is the value of each switch. It starts at 1 and doubles from right to left. At the moment all of the switches are set to 0 which means they're off. Which means we don't add anything to our total.

Let's turn on some switches and see what happens:

| 128 | 64 | 32 | 16 | 8 | 4 | 2 | 1 |
|:---:|:--:|:--:|----|---|---|---|---|
|  0  |  0 |  0 |  0 | 1 | 0 | 0 | 1 |

We've turned on two switches now. One switch is worth 8 and the other 1. So this binary number is worth 9.

> EXERCISE: Write the binary for 36. What is the largest number you can have with 8 bits?

36 in binary is:


| 128 | 64 | 32 | 16 | 8 | 4 | 2 | 1 |
|:---:|:--:|:--:|----|---|---|---|---|
|  0  |  0 |  1 |  0 | 0 | 1 | 0 | 1 |

The largest number you could have with 8 bits is ... you guessed it, 255. This is why our largest IP is 255.255.255.255.

Obviously we could add more bits to get bigger numbers but IPs use 8. So that's what we've got.

Ok back to the subnet mask.

The subnet mask is another IP address but it's one that's used to tell you how much of an IP address is considered a subnet identifier or floor number in your hotel. Think of it like a plaque on the wall.

A typical subnet mask is:

```
255.255.255.0
```

This works like this. If you were to convert his in to binary you would have:

```
11111111.11111111.11111111.00000000
```

This tells us that the first 24 bits of any IP address in our network will tell you whic subnet it's in. Basically anywhere there's a 1. It's really that easy.

Let's try some examples to make sense of this.

Are these two IP addresses in the same subnet if:

```
A - 100.10.10.0
B - 100.11.5.0
Subnet Mask - 255.255.0.0
```

To check this we turn all three IPs in to binary and then use the subnet mask bits to decide how much of the IPs should match if they're in the same subnet:

```
A - 01100100.00001010.00001010.00000000
B - 01100100.00001011.00000101.00000000
SM- 11111111.11111111.00000000.00000000
```

Hopefuly you can see that the two IPs don't match in a every slot that the subnet mask is switched on. They're very close but the 16bit is different and the mask says that all 16 have to be the same.

It's pretty easy to see this if we always fill up whole octets with 1s like this:

```
IP - 255.255.255.0
Binary - 11111111.11111111.11111111.00000000
```

It's always useful to convert the mask to binary especially if we don't fill an octet:

```
IP - 255.192.0.0
Binary - 11111111.11110000.00000000.00000000
```

With the above subnet mask it would be hard to see that the following two IPs are, in fact, in the same subnet:

```
100.200.10.10
100.198.10.10

Binary versions make it clearer that they are
01100100.11001000.00001010.00001010
01100100.11000110.00001010.00001010
```

## CIDR blocks

There is one more far simpler and more common way of doing the same thing as a subnet mask called a CIDR block.

CIDR stands for Classless InterDomain Routing Block. Which is a horrible name for a simple idea.

All we're doing with a subnet mask is saying how many bits at the start of an IP make up the host ( the subnet or floor identifier ). So why can't we just use a number? Well, we can!

192.168.0.0/16

This is the same as a subnet mask of:

```
255.255.0.0
```

Simple as that.

## Subnet size

It might have occurred to you by now that larger the host portion of an IP address is the smaller the number of IP addresses we can have in that subnet.

For example ``192.168.10.0/24`` only allows the last octet to change and only allows us to have 256 different IP addresses in our subnet.

``192.168.10.0 - 192.168.10.255``

When choosing the size of your subnet keep this in mind. You can think of this as do you want a fat hotel with fewer floors and lots of rooms per floor and lots of floors with fewer rooms  per floor.

## IPv6

We won't cover IPv6 in any depth but at it's root it uses bigger numbers in the octets to give us a larger range of possible IPs. It uses Hex values to encode them. 

For now it's enough to know that it's not widely used.

## Routing between subnets

So now that we know how to identify subnets we can try sending messages between them ( between the floors ).

Let's say we want to send a message from room 204 to room 106. We check the CIDR  block and find that room 106 does match our floor. It must be on another floor. So we don't know how to get there. 

We walk down the corridor to the lift and ask the bell boy if he knows how to get to room 106. He looks at his lift buttons and says "yeah that's on the first floor, I can show you how to get there". 

This is called routing. You have a router at home and we have one at the office. They connect all the subnets and computers together.

Inside a router there is table of addresses that it knows how to deliver messages too called, a route table.

In our example the lift is the router and the bell boy is the route table because he knows where everything is.

A route table is literally just a table with two columns. One that contains CIDR blocks and another that contains network interfaces ( the things that control and distribute messages on our floors ).

| CIDR           | Network Interface |
|----------------|-------------------|
| 192.168.0.0/16 | local             |
| 0.0.0.0/0      | ig-0001           |

The first entry in this table tells us that anything that starts with 192.168 is in our hotel or is one of the subnets connected to our network. So the bell boy should be able to get you there.

## Default Gateway

> QUESTION: What does the second entry do?

If you're feeling strong with the CIDR blocks you might realise that 0.0.0.0/0 means "match anything and everything". Precisely 0 bits have to match for this CIDR block to apply. So this is a catch all for everything that the route table doesn't recognise. The route table goes from top to bottom and if it doesn't find a match it reaches the default gateway. 

For this entry we've pointed at the Internet Gateway which is fairly common practise. You can think of the internet gateway as the concierge at the front desk. What we're saying here is... the room you're looking for is not in this hotel. 

So the concierge goes to the global network of hotels to help you find where it should go. This global network is the internet. The internet is in fact a network of networks!

## Network Address Translation

So our hotel has room numbers. But is our hotel the only one in the world that has a room with number 204? Definitely not. That numbering system works well when you're inside the hotel. But once you get outside the hotel the room numbers don't make sense. They're not unique anymore.

These are private IPs. They don't mean anything outside the hotel.

So how could we, for example, send a message between hotels. Or in other words between networks. Which is after all, what the internet actually does.

We need someone that knows how to get the messages between the two hotels and then once it gets there we need a way to tell the concierge at the other hotel which of his room numbers it's going to.

In the real world we would probably use the street address of the hotel to send the message.

So I could send a message from:

1 Hotel Street, Room 204 to 5 Hotel Street, Room 395

The trouble with doing this with networks is that we don't have a second way to address things. We have just have IPs.

So here's what we do. We give a single IP to our internet gateway. This is called a public IP. And everyone that wants to send a message to any room in our hotel first sends it to this public IP. 

Once it arrives at our hotel we need a way for the the people in the post room to figure out which room it should go to. For this we need to translate from the public network address to a private network address. This is helpfully called, Network Address Translation or NAT for short. Our network has another device called a NAT Gateway that will handle this for us.

It's actually easier to understand with a real network example.

Imagine you are using your computer on your home network and you want to send a request to the BBC website on their public IP address 212.58.244.69.

You send out a message and your machine realises that it's not an IP that's in your subnet. So it sends it to the router. The router doesn't recognise it so it sends it to the default gateway which is the NAT gateway built in to your router.

The NAT gateway **does** know where this IP address is and it sends out the request for you through the internet gateway. It attaches to it's request it's own public IP so the BBC webserver knows where to send the response back to. 

The request goes out and the BBC sends back the response to the IP of the internet gateway.

The problem is the NAT gateway can't just send the response back to you on the private network. There could be a lot of other people on your network sending requests to the BBC. So it needs a way to keep track of who sent the request.

This is where the NAT Table steps in. It keeps a track of who sent each request. The way it does this is it assigns you a random port number before it sends the message out. It then asks the BBC website to respond to your request on that port. That way when the response comes back it knows it was for you and it routes it back to you.

Simple as that!

The simplest way to think of a NAT Gateway is that it's a device that allows requests to come out of a private network and responses to come back. But it doesn't allow incoming requests.

We call this a semi-private network. A fully private network wouldn't have access to the internet at all!

A public network however has IPs for each machine that everyone in the world can send requests to.


## Summary

You just learned:

* What a network is
* How IPs are created
* How subnets are addressed
* How requests are routed between subnets
* How an internet and NAT gateway allows for connections between networks



