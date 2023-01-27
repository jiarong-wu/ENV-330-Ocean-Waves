# Using Jupyter on Adroit
Go to [MyAdroit Web Portal](https://myadroit.princeton.edu/).

Under "Interactive Apps", start a "Jupyter" server. Specify the number of hours you would like the server to 
stay active, number of cores (typically 1), and use default settings for all the other options. Click "Launch".

You might need to refresh the webpage to see the session being active.

![Screenshot of MyAdroit](https://user-images.githubusercontent.com/44583114/214600028-99acda37-c68c-4534-a0e9-4971d48665b3.png)


After that, click "Connect to Jupyter", and you should be taken to your home directory on Adroit `/home/yournetid`.
You can create your own folders and move the jupyter notebooks and data over from the shared `/home/ENV330` folder 
as you like and start working on them!

# Moving things around on Adroit
On Adroit, you have your own `/home/yournetid` directory as a student user. If you click on the "Files" tab, you will be 
able to see and manipulate your files from there. `/home/yournetid` is your home directory, your safe place. You can write and delete things as you want, given that you do not exceed the given file storage quota.
(You can check your quota under the "Files" tab as well.) You also have your own `/scratch/network/yournetid` directory, 
which acts as a temporary storage place, as the name suggests. You can use it to store temporary large files or files generated during simulations 
(which we might need later in the semester).

There are two ways you can move files into your Adroit home directory. One is to download them from Canvas onto your personal
laptop and then upload them through the "Files" interface. Similarly you can download files from Adroit to your personal laptop
and then submit them on Canvas. The other way is to move files directly from the shared folder `/home/ENV330` to your own home
directory. This might prove easier especially when we want to move large data files. The shared folder `/home/ENV330` is a folder 
that also sits on Adroit but you only have read permission to it. We will have a copy of the Canvas materials inside that folder.

To copy from the shared folder, you need the command line copy [cp](https://www.ibm.com/docs/en/aix/7.1?topic=files-copying-cp-command). 
Click "Open in Terminal", type 

`cp full_path_of_the_file_being_moved full_path_of_destination` 

and press enter, e.g. 

`cp /home/ENV330/notebooks/assignment0.ipynb /home/yournetid/`

And you are good to go!

