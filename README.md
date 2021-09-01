# covetcc-reversal
This is a reversal of a pay to cheat called covet.cc
 
# Mitigating authentication
The cheats loader made use of the authentication service c0gnito which can easily be bypassed without much effort giving the attacker access to its payload.
![bypass_auth1](https://user-images.githubusercontent.com/89882326/131600122-ddf7b007-9d22-4281-ba6e-5acfa7a9d1cb.PNG)
![inside](https://user-images.githubusercontent.com/89882326/131600128-ca5f9129-56a0-4d36-acb1-3a921da616f7.PNG)

The secret key for this clients c0gnito api implementation.
![secret_key](https://user-images.githubusercontent.com/89882326/131600226-ef197006-fe07-45f1-8b16-5a9d4406e2ff.PNG)

# Image protection
They have chosen to use VMP 1.60-2.05  to virtualize their image, after dumping the image we get to get into the proper analysis of the loader. Although VMP is good enough to prevent quick byte patches to the image it doesn't do a whole lot to hinder the attacker from reversing the loader. Other than what VMP had done none of the strings were encrypted, the actual cheat logic is contained within their loader so we don't have to bother with having to get it from the server; the drivers image as mentioned earlier is stored on discord 
![driver_url](https://user-images.githubusercontent.com/89882326/131600521-d01bebb8-8136-47ff-a4e9-b77122aa81c7.PNG)
https://cdn.discordapp.com/attachments/784216980424884234/797620961646084137/driver.sys
sadly for them this isn't the only way someone can obtain their driver image because to get their driver to the local machine they use URLDownloadToFileW to download the file then store it in C:\\Windows\\IME under the name u_arent_cool_for_getting_this_driver_id_like_to_see_you_reconstruct_communcation.sys to add to that this downloaded image isn't protected one bit and requires zero effort to analyse.
![driver_dir](https://user-images.githubusercontent.com/89882326/131600553-60f6a179-8019-4da6-80c1-2c1ddddc612d.PNG)

I also ran my PE appropriator https://github.com/j7cb1/PE-Appropriator to see if their loader contained any other images within it that could be of use; which it did contain iqvw64e.sys which we can assume is due to https://github.com/z175/kdmapper so their are now 3 ways to quickly and easily obtain their driver. Which isn't too ground breaking as it is clear the developer didn't put a whole lot of effort into keeping it out of the attackers hand. 
![pe_appro](https://user-images.githubusercontent.com/89882326/131600646-9335161f-f2d8-41cd-a70d-ea573a6160d3.PNG)


# Driver communication
Starting off with the driver query types; they first initialize their hook on the driver control dispatch method to securely by using this method from UC https://www.unknowncheats.me/forum/anti-cheat-bypass/372215-driver-control-dispatch-hooking-method.html the way the logic it is by awaiting the process r5apex.exe to be active and then they initialize communication by setting a global bool in the driver to true which the drivers ioctl handler rely's on to complete the driver queries. The other queries use the same IO control code if I remember correctly (youll be able to locate the IO control codes for all the other driver primitives)

![io_controller1](https://user-images.githubusercontent.com/89882326/131600657-edc040eb-e489-4f2e-a738-f6c6f13c1443.PNG)
(top image is usermode bottom image is kernel)
![driver_iohandler](https://user-images.githubusercontent.com/89882326/131600665-d2a92ef1-8a81-4420-8b58-f76c9cb9d2e7.PNG)

These are psuedo query types I made based of this analysis 
![querys](https://user-images.githubusercontent.com/89882326/131600680-06fc1f38-9a18-4528-a83f-47e5cddb7a9f.PNG)

Some of the query codes they use for the driver, I'm not 100% sure about init as I am finishing this writeup at a later date so I can't remember if I am right about that, due to this being a later writeup Ive kinda lost the reversal of the drivers read and write primitives so I cant send them but basically once you find the drivers ioctl handler then you should be able to find the primitives by using the query codes I found. (Also idk if Ive added but they use KDmapper to map the driver).
