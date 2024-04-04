# Network-Traffic-Analysis-of-Android-apps-using-MITMproxy-and-Frida
Abstract: With the overwhelmingly increasing number of applications in android marketplace, i.e., google play store; over time with the development of android operating system and user experience; the aspect of vulnerability has risen constantly. Over time, as applications have been developed along with android OS to keep up the pace, security concerns have risen. And in this era of the internet, most of the threats pose from there. In our daily lives almost every app is dependent on the internet. My analysis focuses on the part of android applications where they make connections to different web and third-party servers through the internet during performing any specific or common tasks. The android framework architecture diagram (Figure 1); would be appropriate to provide an overall explanation of my target. I will be taking advantage of the runtime layer to monitor API calls made by android apps within the android framework layer. This analytical report contains detailed statistics about how concerning unauthorized data transfer can be.

![Screenshot 2024-04-03 190242](https://github.com/Pegasus-01/Network-Traffic-Analysis-of-Android-apps-using-MITMproxy-and-Frida/assets/48630058/b7758444-3a6b-47cc-9a5d-540134b4c5aa)

Severity of Threat: Mobile applications pose a serious threat to user privacy and security through extensive data collection practices. Unchecked gathering of personal information and device details by apps allows unwanted interception or exposure of sensitive credentials, messages, and account specifics. This erodes user trust and transparency, making apps seem deceptive. A particular concern is connection to ad servers and unencrypted data transfer to external parties. While it may not seem impactful from one user’s view, such practices actually enable invasion of privacy through targeted advertisements and even physical mailers. Apps accessed on a daily basis collect extensive details like location, usage patterns, device details etc. and share these with advertisers to profile users. This makes the app experience less responsive. Thus, comprehensive analysis of network traffic is necessary to highlight the severity of privacy violations by mobile apps and argue for more stringent controls around user data sharing. The goal is to preserve user rights against obscure apps that secretly sell out personal information for commercial interests. Users must be made aware of these practices so they can make informed choices about app permissions while developers and app stores also need to prioritize transparent data collection policies.

Tool Overview and Approach: The core tool I used for network traffic interception and inspection is MITMproxy, Frida and Objection. MITMProxy, or Man In The Middle Proxy is a tool which gives the user the ability to intercept and inspect a connection between a client and a server. By rerouting connections through MITMproxy, I captured and analyzed requests and responses to study app behavior. Another necessity is bypassing SSL encryption which apps employ for security. For this, I use Frida and objection. Frida is a dynamic analysis tool which is used to analyze app behaviors as well as injecting scripts in runtime. Objection is a runtime analysis toolkit powered by Frida. I used Frida in embedded mode to inject “frida-gadget” which is compulsory for getting runtime access for third party applications in rooted/jailbroken devices. Then I used objection to bypass the SSL pinning which is required by these apps to verify certificates while making network connection requests. This granted MITMproxy visibility into encrypted HTTPS traffic as well.

![Screenshot 2024-04-03 190325](https://github.com/Pegasus-01/Network-Traffic-Analysis-of-Android-apps-using-MITMproxy-and-Frida/assets/48630058/45dd506b-8434-436f-9787-433d90ef7a33)

The diagram of my setup architecture given above (Figure 2) can be used to demonstrate my approach from a reader’s point of view. The workflow I followed is: inject frida-server, frida-gadget and identify the app process using Frida, use Objection to bypass SSL Pinning in runtime, then run the app to generate network activity that MITMproxy can intercept. Also, changing the proxy of the emulator according to MITMProxy is necessary to capture packets. I used the CLI based version of MITMProxy to inspect requests and responses as it gives a better view od data regarding total number of packets.

![Screenshot 2024-04-03 190357](https://github.com/Pegasus-01/Network-Traffic-Analysis-of-Android-apps-using-MITMproxy-and-Frida/assets/48630058/4368c35a-b4b9-43f9-987b-3d6df3b00df7)

Analysis: My analysis contains a study on 10 android apps; all on their latest available versions.

![Screenshot 2024-04-03 190424](https://github.com/Pegasus-01/Network-Traffic-Analysis-of-Android-apps-using-MITMproxy-and-Frida/assets/48630058/0b78d6bc-f06e-4097-9cef-d0f3b44f6d4a)

Bitdefender is a widely used mobile antivirus application. BJ’s is the applications for a very famous wholesale market in the US. CNBC is the mobile app for the famous news channel itself. Doordash is one of the most famous food delivery apps in the US. Duolingo is a very popular apps for learning new languages in an interactive way. Ebay is one of the most popular third-party seller-buyer chain application. Mcafee is another popular antivirus which I have taken in consideration because almost every large brand offers free subscription to this upon buying a new device. Talking Tom 2 is a very popular game which is especially targeted for kids. Spirit is one of the most famous low-cost airline carriers in the US and Zillow is a very popular app for finding rental properties. As all of the apps taken under the analysis make connections to the internet; I have categorized the detailed analysis into 5 sections. Table-1 contains a brief categorical overview of the overall analysis and Table-2 contains how impactful/user engaging these apps have been so far in google play store along with ranking of severity which I concluded from this analysis. And Table-3 gives a comprehensive detail about the traffic (own server/3rd party etc.) created by the analyzed applications. I captured traffic using MITM Proxy for ≈5 minutes per each application. I did not use any fixed sequence of actions over the app to make the analysis more focused towards randomized user interactions. Findings are as described below:

![Screenshot 2024-04-04 124402](https://github.com/Pegasus-01/Network-Traffic-Analysis-of-Android-apps-using-MITMproxy-and-Frida/assets/48630058/28e4f493-bd01-48c0-ad4f-bb7914a721a5)

A. Connection with Ad-servers: All of the apps have shown connections with ad servers under monitoring with MITM proxy, some of them even with over 900 packets; but the most significant numbers of concerning connections are traced from Doordash. A categorical statistic about the network packets; both quantitative and qualitative, is listed in Table-3.

Considered a very popular antivirus used by millions of android users; Bitdefender fails to hold its grounds. It connects with 3 ad-servers: “assets.adobetm.com”; “dpm.demdex.net” and Bitdefender’s firebase analytics server. In my analysis time of 5 minutes; total number of packets were 220, among which over 40 were sent to advertisement and analytical servers. The most number of packets were sent during performing the “Malware Scan” inside the app: approximately 120 in 9 seconds, which were a combination of third-party traffic and traffic to own server. One of the main concerning facts is that, it sent a packet each to the 3 mentioned servers containing all the permissions of each applications’ permission over the system; including even the system apps like “messages”; “Chrome” etc. As an antivirus; it is possible to send these data to its own servers but sending it to third party servers raises major security concerns. The most privacy related vulnerability I found in this app was transferring of fingerprint data to its own server. Although in my analysis; as I am using an emulator; that placeholder was empty; but in physical devices; it indicates a major privacy violation.

BJ’s made connection with only 2 ad-servers: Google’s “doubleclick” and “edgedl.me.gvt1.com”; but it transferred packets to third parties with the rate of more than 4.5 times than its own servers. I analyzed a total of 657 packets and among them over 550 were sent to third parties. Upon searching any item inside the app; an encrypted arbitrary binary data stream was sent to its own analytical server. While the most traffic flow creating action was clicking the icons in the bottom menu; in idle condition it made 104 packet transfers within 1 minute; most of them being to the mentioned ad-servers about the changing device state. Unfortunately, I was unable to monitor packets upon logging in because it needs a subscription to log in. Although, upon clicking the “login” icon, it sends a packet to “doubleclick” about the current user activity with a specific phrase about current user activity on the logging page.

CNBC made the most network traffic; a total of 956 packets in 5 minutes; while the triggering GUI action being “refreshing”, which created packets on a rate of approximately 91/second. It made connection with 3 ad-servers: “kvtracker”, “control.kochava.comn/track” and Google’s “doubleclick”. I recorded an approximate GET/POST ratio of 4:1 with a total of 167 packets transferred in idle condition in 1 minute. In every 15 seconds, it transferred a packet to “kvtracker” with changing device state info in the payload. Just after starting the app post installation, without any action; it sent a packet to an IP address: “162.247.241.4” with encrypted content in payload. Considering a news app; it is expected to create a heavy network traffic but the content it transfers over the web; is concerning.

Doordash is the most vulnerable application in this analysis according to me. It is just a food delivery app but the way it handles data, raises major security concern. Just upon launching the app; it made 16 POST requests, among which 13 were to ad-servers: 4 packets to “iguazu-edge.com”, 2 to “recaptcha.net”, 4 to “api.segment.io” and 3 packets to “sdk.iad-06.braze.com”; without even clicking any button. Upon clicking the “Continue With Email ID” button; it sent a PUT request to “api3.siftsciences.com” along with a normal POST oAuth token to its own server. The concerning fact is that, the PUT request contained not only device ID but also the device’s MAC address along with network state, battery level, storage details and device state. It made a total of 11 PUT requests to this server and all of them contained similar data. Doordash also sent some POST requests to “graph.facebook.io”, which is Facebook’s analytical server and “o17585.inget.sentry.io”. This app is made over the React DOM architecture so it does not need to make very high flow of traffic to keep the UI/UX intact. I monitored a total of 349 packets with an approximate GET:POST ratio of 1:2, among which, over 275 was with third parties. There were no specific triggering GUI actions but it transferred the most amount of data to third parties just after logging in. It raises major encryption related concerns too; which I have discussed in detail in the “Encryption in network traffic” section.

Duolingo can be stated as the safest app in my analysis. As it is a free app with “in-app purchases” option; I expected it to make a lot of third-party transfers. Not only it did not make connections to ad-servers; but also it transferred very few device information to its own servers. It made a total of 357 packet transfers in 5 minutes, all of them being from its own AWS server. With a GET/POST ratio of approximately 6:1 (as it fetches options from its server); there was no triggering event recorded.

Ebay is the second most vulnerable application in my analysis. Apart from the encryption related concerns; it made connections with a total of 2 third-party ad servers: “api.forter.com” and “cdn.quantummetric.com”. But it transferred the most packets to its own advertisement and analytical servers over the analysis of 138 packets with a GET: POST ratio of 2:1 with the triggering event being “Search for an item”. Just upon starting the app; it sent a packet to “api.forter.com” which contained device state information and a specific string: “signupformarketingcommunication: true” in its JSON payload; even without clicking anything. Apart from the information containing packets, in every 30 second, it sent one POST packet to “api.forter.com” which has some data inside the payload which MITMProxy was unable to parse. Upon closing the app, it sent 3 additional packets to “cdn4.forter.com” which was marked as “analytical survey”.

Mcafee, being a well renowned multi-OS antivirus; does not handle user data properly. Upon starting the app, it sent 4 packets to “sdk-01.moengage.com” with all the device information. Additionally, upon clicking the “Do Not Share Data”, it sent a packet to the mentioned server with all the device information including GAID. Upon signing up, it sent a packet each with similar information to “bewmvq-dlsdk.appsflyersdk.com” and “events.split.io” along with “moengage.com”. Upon analyzing a total of 126 packets, I recorded the triggering event as visiting the user profile; which created 19 POST requests among which 3 of them were to the mentioned ad-servers with the current and previous subscription history included in the payload. Additionally, it sent one packet to each servers mentioning “post-analytics survey” just after closing the app.

Talking Tom 2; being just an interactive game, it does not even need much network interaction. But just upon starting it created a staggering 107 packets; 32 of which were to POST requests to “apps2.outfit7-analytics.com” which is its own user engagement server and 7 packets to some random IP addresses containing all device information. I analyzed a total of 956 packets within 5 minutes with a GET:POST ratio of approximately 1:3. During that time it made connections with 8 ad-servers: 3 ad-servers owned by amazon, facebook analytics, crash reports to “ms.applovin.com”, “cdn2.inneractive.mobi”. “sdk-hb-cfg.smaato.net” and “c.tpbid.com”. It made a total of 213 GET and POST requests in idle time of 30 seconds. There was no triggering UI action inside the app; it was a constant rate.

I expected Spirit to make a lot of marketing related communication as it is a very low-cost airline carrier but it did not make connections to many ad-servers. Upon starting it sent a POST request to “cdn.brach.io” and later upon closing a packet to “dynatrace.io” with just location, timestamp and android version. I analyzed a total of only 28 packets in 5 minutes without any triggering event; and with a GET:POST ratio of 1:1 where the POST requests were to it own servers and it included only the flight information which were searched.

The last application in my analysis was Zillow. Being a housing finder, it is expected to make a lot of connection with ad-servers, following the market trend, at it did. I analyzed a total of 298 packets with a GET:POST ratio of 1:4; where it made connections with 9 ad-servers: “api.apptentive.com”, firebase analytics, “doubleclick” by Google, “graph.facebook.com”, ingest.sentry.io”, “app.us.adjust.com”, “cs.zg-api.com” and “events.split.io”. The triggering UI action was visiting “Renter’s Profile”; during which, each updated values (e.g. monthly rent, no. of rooms required etc.) were sent to each ad-servers. I gave it an idle time of 30s. during which it made a total of 30 GET and POST requests.

Most of the packets were POST request in which payloads for all the apps except Spirit and Duolingo, contained device information: OS version, device location using 3 axis accelerometer data, total device memory, available memory, all the information about the device’s display (display height, width, pixel density etc.), whether it’s rooted or not, whether it has ADB sideload enabled or not, connection type of the connected network (Wi-Fi or Mobile network), SD card’s availability, battery charging state; including the GAID (Google Advertising ID). The concerning factor is the authentication packets which I have discussed later in this report. Table-4 contains a more detailed categorical overview of which data was transferred by which of these applications through the payloads.

![Screenshot 2024-04-04 124509](https://github.com/Pegasus-01/Network-Traffic-Analysis-of-Android-apps-using-MITMproxy-and-Frida/assets/48630058/1f4b49ff-c29b-4bd3-bbd5-a16b1025e5a8)

B. HTTP/HTTPS Connections: In my analysis, all of the applications made connections over only HTTPS; except very few image viewing requests to their own servers in case of CNBC and Duolingo. As this is very common as these apps fetch data (mostly images) to perform UX actions and not a very severe concern; it does not raise much concern.

C. Encryption in network traffic: In my analysis, some of the applications have shown promising aspect of how they handle user data but also on the other hand, some have shown serious privacy concerns.

Bitdefender’s traffic was almost 80% encrypted with GAID and location in plaintext. In BJ’s, it required a subscription, because of which I was unable to log in and monitor authentication packets. In the other packets, only the device ID, session ID and GAID were encrypted. In CNBC, it was very similar to BJ’s. No login was required and in the other packets only the device ID was encrypted. Mcafee encrypts only the device information in its traffic. In case of authentication packets, it uses OTPs so encryption is not applicable. In case of Talking Tom 2, none of the traffic was encrypted. Zillow also does not encrypt its packets. All of the information was in plaintext.

Duolingo and Spirit has been promising in both connection with ad-servers and encryption. In Duolingo, authentication was not necessary and in the other packets, only the android version and device model name were in plaintext. In Spirit, it encrypted everything: email ID, password, device ID. As it uses a token-based payment system, it also does not require adding a credit card inside the app.

On the other hand, Doordash and Ebay has shown the most concerning factors in encryption also. Both of the apps’ transfers authentication packets in plaintext which included email ID and password. Ebay is more concerning from one aspect; it sends the credit card information in plaintext (Figure-3). This is a major threat as it can be intercepted and with all the credit card information, even the security code in plaintext, user privacy is basically nothing here in this case.

Suggestions for Mitigating These Risks: From the analysis, we can clearly see how these apps violate user privacy through either requiring extensive permissions later while using the app or obfuscating terms and conditions while installation. A lot of third party analytical or advertisement servers get access directly to user data and device information which they use for targeting ads to app users. These apps also belong to a large user base. They can use these data to increase their revenue with the cost of user privacy. I would like to suggest security improvements divided over 3 aspects:

A. Users: Users should be selective in downloading apps and favor more reputable developers and should not use third party antivirus or ram-cleaning applications which require extensive permissions over the system (especially free apps which offer “in-app-purchases”). Reading the whole terms and conditions page is always recommended because some apps make the user grant permission by just making them accept the terms and conditions. Even if an app is allowed permission, the user should only permit for very necessary options using the permission manager in android. Also, use of ad blockers and analytics opt-out platforms are encouraged. I would also like to suggest adding descriptive comments in google play store if a user finds an app violating privacy. This would make other android users aware before downloading the application. Also, as some of the major applications are transferring email id, password and credit card information unencrypted; I would recommend using spare email IDs and a separate password for each application along with not adding credit cards. Instead, using of prepaid cards is recommended for saving card information in applications. This will prevent attackers from using those credentials in other websites. Also users should not store credit card information in these applications for safety against identity theft and unauthorized payments from credit cards.

B. Developers: Developers should minimize data collection and transmission, only collect what’s needed for core app functionality. Also they should add preventive measures so that the app would not make HTTP connections. Developers should also avoid hiding permission requirements within long pages of terms and conditions. Another major improvement would be encrypting all the credentials regardless of the application’s functionality and UI/UX.

C. Marketplaces: Marketplaces like google play store should enforce a more strict vetting process where apps offering “in app purchases” have to go through additional analysis; as mainly these apps make business with third party servers to get revenue even from their free tier versions. Considering that they would not remove their own analytical server; Google play should at least restrict apps which make connections with third party analytical servers. Google’s bouncer for the play store should use stronger privacy standards to prevent apps getting passed by with these kinds of vulnerabilities.

Unauthorized data collection is a very prominent problem as almost every company uses this to show advertisements; including banks, financial institutions, software manufacturers, hardware companies etc. With market trends it is unlikely for organizations to take user data from apps such as Zillow. Organizations use these data in a major scale to uplift their profit. So, it cannot be mitigated at once. But taking proper steps with security standards will alleviate this over time.

Future Works: In my opinion; if the ad-server traffic can be blocked with MITMProxy in between the GET and POST request, that will open up a new aspect of study and analysis as it might affect the UX and overall application performance. Also, analyzing the application’s traffic during a stress test would be a very competitive task for developers and analysts. Along with that, I encountered some problems while using complex applications like Instagram in the emulator. It does not allow me to use this app in the emulator and terminates the account. If this was a longer-term project, I would be looking forward to analyzing these mentioned areas; probably with access to a physical device. Along with inspection of some applications with heavy sensor usage (such as applications with face unlock permissions or fingerprint unlock permissions and camera, mic, NFC etc.) to see if and how they use sensor data over the internet. As during this project, I could not get a physical device; I had to refrain from analyzing these aspects.

For the installation steps and setup guide; please read my article: https://medium.com/@rudranilmaity01/how-to-setup-application-network-traffic-monitoring-using-mitmproxy-objection-frida-f39990baa52d
Will post more regarding network analysis and vulnerability scanning soon. Stay tuned!!
