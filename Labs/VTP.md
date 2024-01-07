# VTP Lab

## Table of Contents
  * [Part 1](#part-1)
  * [Part 2](#part-2)
  * [Part 3](#part-3)
  * [Part 4](#part-4)
  * [Part 5](#part-5)

## Intro
The premise of the lab is as follows:

![image](https://github.com/Jacob-Hegy/Projects/assets/85857129/74ba8e2f-bf4b-4842-bd42-4464a49947b9)

## Part 1

The first part asks us to configure the ports connecting the switches to one another as trunk ports as well as disable DTP on each of them. This is simple enough, we'll start with SW2 since it has two ports connecting it to other switches.

We'll begin by opening up the CLI on the switch, entering global configuration mode, then selecting our first interface, g0/1.

![image](https://github.com/Jacob-Hegy/CCNA-Notes/assets/85857129/0c3fbe33-e8e4-4236-a323-36c35a4be9e4)

Once we've selected the proper interface, we'll simply change the port from an access port to a trunk port with the command ```switchport mode trunk```. Additionally, we'll disable DTP with the command ```switchport nonegotiate```. We'll repeat the same set of commands on g0/2.

![image](https://github.com/Jacob-Hegy/CCNA-Notes/assets/85857129/163fea5a-171b-49c9-9b02-eec328a29067)

We can check to make sure that the commands worked by backing out of global config mode and using the command ```show run | section GigabitEthernet0/[1, 2]```.

![image](https://github.com/Jacob-Hegy/CCNA-Notes/assets/85857129/c4901663-f4f3-44e8-8bb7-d4d24734fe84)

Looks good! We'll go ahead and do the same to g0/2 on switches SW1 and SW3.

![image](https://github.com/Jacob-Hegy/CCNA-Notes/assets/85857129/a219f239-7456-45cf-bc7d-46d26010d6f7)

![image](https://github.com/Jacob-Hegy/CCNA-Notes/assets/85857129/d9087fb4-d7ce-4a84-831f-aeb1c9e5126b)

## Part 2

Moving onto part 2, we're asked to add SW1 to the VTP domain "CCNA", create VLANs 10, 20, and 30 on SW1, and check if the VLAN configuration has been distributed to switches SW2 and SW3.

Jumping back to SW1, we'll go ahead and add it to the proper VTP domain by reentering global config mode and configuring its domain with the command ```vtp domain CCNA```.

![image](https://github.com/Jacob-Hegy/CCNA-Notes/assets/85857129/a49865af-2144-4014-b442-75dfda675619)

Once we've done that, we'll go ahead and create VLANs 10, 20, and 30 with the commands ```vlan 10```, ```vlan 20```, and ```vlan 30``` respectively. Then we'll just make sure that they were created properly.

![image](https://github.com/Jacob-Hegy/CCNA-Notes/assets/85857129/c7d8eb15-e7f3-4bee-8f27-15d67b4ad813)

All set on those, now to check on switches SW2 and SW3.

![image](https://github.com/Jacob-Hegy/CCNA-Notes/assets/85857129/34ba3cd9-b9d2-41cd-b553-032c881bfb25)

Sure enough, the VLAN databases on SW2 and SW3 have been updated with the VLANs created on SW1.

## Part 3

We're gonna tweak the VTP configuration on SW2, setting it to VTP transparent. Additionally, we'll go ahead and add VLAN40 on SW2 and observe the databases on SW1 and SW3.

Opening up SW2's CLI, we'll once more delve into global config mode. This time, we'll issue the command ```vtp mode transparent``` then verify the changes went through with ```do show vtp status```.

![image](https://github.com/Jacob-Hegy/CCNA-Notes/assets/85857129/2b7df47a-b822-4a60-80de-d9f4ed45264f)

The VTP mode has changed properly, so we'll go ahead and add our VLAN.

![image](https://github.com/Jacob-Hegy/CCNA-Notes/assets/85857129/3a4fe41d-bf08-4e9f-83b1-56fab3132976)

Looks good, now lets check the VLAN databases on SW1 and SW3.

![image](https://github.com/Jacob-Hegy/CCNA-Notes/assets/85857129/4741cf65-904f-4cf1-b73e-78c48652ca8c)

As we can see, neither database contains VLAN 40, so what gives? Well thinking back to the VTP modes, transparent differentiates itself from client and server by maintaining an independent database from its domain. That is to say, it doesn't advertise its database, nor does it sync with other advertisements within its domain (although it does forward them). This explains the lack of VLAN 40 on SW1's and SW3's databases.

## Part 4

We're messing with another of the switches' VTP configurations, this time changing SW3 to client mode. This is done in a similar manner to what we did moments ago for SW2, except the command this time is ```vtp mode client```.

![image](https://github.com/Jacob-Hegy/CCNA-Notes/assets/85857129/0a142845-2bd4-4678-9968-de4066d043f2)

Okay, we're set, now to test the switch by trying to create VLAN 50. However, when doing so, we're met with the following:

![image](https://github.com/Jacob-Hegy/CCNA-Notes/assets/85857129/d2ac89a3-9c9e-4a88-aad9-eb81c7fc295f)

This goes back to the characteristics of the three VTP modes. Client mode lacks the capabilities of server and transparent mode in that it cannot add, remove, or modify VLANs in any way. It's essentially a passive player in VTP, listening to advertisements, syncing their databases, and forwarding the advertisements of others. They do advertise their own database, but that's the only active component of their role.

## Part 5

Finally, we have the job of properly configuring each of the access ports on the switches with the proper VLANs as well as checking to see if DTP is enabled or not on them.

What we'll do on each port is execute the command ```switchport mode access``` as well as ```switchport access vlan _``` (where _ is the name of the VLAN).

![image](https://github.com/Jacob-Hegy/CCNA-Notes/assets/85857129/d5059a00-63c7-40be-aac2-6c5bc0cb712f)

Lets take a look to see if DTP is still enabled on interface Fa0/1's switchport with the command ```do show int Fa0/1 switchport```.

![image](https://github.com/Jacob-Hegy/CCNA-Notes/assets/85857129/0c3444f5-1e02-4bf4-84c2-0335ab6dc285)

As we can see "Negotiation of Trunking" is set to Off. This is because manually configuring a switchport as an access port automatically disables DTP. Thus, we should see that DTP is disabled on all the other ports that we configure as well. We'll quickly setup the rest of the access ports.

![image](https://github.com/Jacob-Hegy/CCNA-Notes/assets/85857129/a199fafd-bb76-49ff-a6bc-c49a51c46879)

![image](https://github.com/Jacob-Hegy/CCNA-Notes/assets/85857129/0eabbbe5-dd15-469a-802c-9fa56c643ad8)

![image](https://github.com/Jacob-Hegy/CCNA-Notes/assets/85857129/f620e2d3-b76c-4bf2-a5b6-64c0bbfd4a7b)

And that'll do it! That's the end of the lab, we've properly configured the trunk ports on each of the switches, set SW2 to VTP transparent mode & SW3 to VTP client mode, and finally we configured all the access ports on the switches with the correct VLANs while ensuring DTP was disabled on them.

Please go check out [Jeremy's IT Lab](https://www.youtube.com/@JeremysITLab) on YouTube, he makes amazing content and he is the source for this lab. He's got a free [CCNA prep course](https://www.youtube.com/playlist?list=PLxbwE86jKRgMpuZuLBivzlM8s2Dk5lXBQ) on his channel that provides invaluable knowledge that I cannot recommend enough.
