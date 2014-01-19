OPEATING SYSTEM PROJECT 2 - Modified Version of V6 Unix File System
===========================================================================================================
START DATE: 11/14/2013
END DATE: 12/3/2013
TEAM MEMBERS:2
Pradeep Anatharaman(pxa130130)
Harish Srinivasa(hxs134430)
===========================================================================================================
PROBLEM AND OBJECTIVE:
To redesign UNIX V6 file system to remove the 16 MB limitation 
 on file size using the given guidelines.
FILE NAME: fsaccess.c
DESCRIPTION: This program creates a simulated environment in which required 
 features(Project 2 requirements) 
are provided to user to access the 
 modified Unix Version 6 file system. It reads a series of commands
 from the user and executes the same. 
===========================================================================================================
 OPEATING SYSTEM PROJECT- Modified Version of V6 Unix File System
Objective: To redesign UNIX V6 file system to remove the 16 MB limitation on file size using the given specifications.
Platform used: UNIX and C
Redesign summary:
Redesign of I-Node structure to support 4 Bytes word size
Redesign of superblock structure 
SUPPORTED COMMANDS: 
1. initfs <path/directory_name> <number_of_blocks> <number_of_inodes>
	SAMPLE: initfs /home/Ap/disk 1000 300
2. cpin <external_source_file_path> <V6_file_name>
	SAMPLE: cpin /home/Ap/read.txt V6-file
3. cpout <V6_file_name> <external_destination_file_path>
	SAMPLE: cpout V6-file /home/Ap/largefile
4. mkdir <directory_name>
	SAMPLE: mkdir v6-dir
5. q
	save(write to superblock) and quit.
	
To execute program use the following commands on Linux server:

First: gcc -o fsaccess fsaccess.c
Second: ./fsaccess
		
===========================================================================================================
FILE SYSTEM MODIFICATIONS
I-node struct:(data type Short to Int to include word size 4 Bytes)
1. unsigned int size1;            
2. unsigned int addr[28];			
===========================================================================================================
Small File Max Size: 56 KB
Large File Max Size: 28672 KB
===========================================================================================================
PLATFORM USED:
Cygwin - Unix API emulator
===========================================================================================================
RESULT CAPTURE FILES:
"Screen shots for Project 2.pdf"
===========================================================================================================
METHODS USED:

I. initialize_fs(char* path, unsigned short total_blcks,unsigned short total_inodes)
--------------------------------------------------------------------------------------
INPUT: 
1. Path where the file system should be created.
2. Number of Blocks the file system needs to be partitioned.
3. Number of i-nodes in the file system.
OUTPUT:"1" for a successfull creation of the file system else "0".
PURPOSE: 
1. Intialize the file system by freeing all the data blocks, unallocate all the i-nodes.
2. Allocate the required Blocks for the i-nodes( One block can hold 16 i-nodes: [2048(Block size)/128(Size of each i-node)]=16

II. void create_root();
--------------------------------------------------------------------------------------
INPUT:null
RETURNS:NA
PURPOSE: To create the root directory of the file system and allocate its i-node.

III. void blockreaderchar(char *target, unsigned int blocknum);
--------------------------------------------------------------------------------------
INPUT: character pointer(passing by reference) to store the contents read in the block and the block number of the block to be read.
RETURNS: NA
PURPOSE: To read string of characters from the data block specified by its block number and store the same 
in the character pointer. Used mainly to read file contents from its data blocks. 

IV. void blockreaderint(int *target, unsigned int blocknum);
--------------------------------------------------------------------------------------
INPUT: Integer pointer(passing by reference) to store the contents read in the block and the block number of the block to be read.
RETURNS: NA
PURPOSE:PURPOSE: To read string of characters from the data block specified by its block number and store the same 
in the character pointer. Used mainly to read block numbers in the block specified in the free array and also used to read block numbers in indirect 
block.
 
V. void blockwriterint(unsigned int *target, unsigned int blocknum);
--------------------------------------------------------------------------------------
INPUT: Integer pointer(passing by reference) to write the contents and the block number to which contents are written.
RETURNS: NA
PURPOSE: To write the contents in the integer pointer to the block specified by the block number. Used mainly to 
write block numbers to blocks.

VI. void blockwriterchar(char *target, unsigned int blocknum);
--------------------------------------------------------------------------------------
INPUT: Character pointer(passing by reference) to write the contents and the block number to which contents are written.
RETURNS: NA
PURPOSE: To write the contents in the character pointer to the block specified by the block number. Used mainly to 
file contents to blocks.

VII. void inodewriter(fs_inode inodeinstance, unsigned int inodenumber);
--------------------------------------------------------------------------------------
INPUT: Structure reference of the inode and the inode number.
RETURNS: NA
PURPOSE: To write inode fields (referred to by the inode reference) to the memory specified by the inode number. Block number is calculated
as follows: 2*2048 + "inode number" * 128.

VIII. void freeblock(unsigned int block);
--------------------------------------------------------------------------------------
INPUT: block number to free.
RETURNS: NA
PURPOSE: Used to free data blocks while initializing free array.

IX. void chaindatablocks(unsigned short total_blcks);
--------------------------------------------------------------------------------------
INPUT: Total number of blocks in the file system.
RETURNS: NA
PURPOSE: Function to chain free data blocks. It performs the following:
1. Chain chunks of 100 blocks at a time and store the values in an array
2. Put this array to begining of every 100th block and this array will be used for 
   setting the values of nfree and free[] array.

X. unsigned int allocatedatablock();
--------------------------------------------------------------------------------------
INPUT: NA
RETURNS: Data block number allocated for file system example(root directory,directories, files etc) 
PURPOSE: To return the Data block number allocated for file system example(root directory,directories, files etc) 

XI. unsigned short allocateinode();
--------------------------------------------------------------------------------------
INPUT: NA 
RETURNS: i-node number of a free i-node from i-node array
PURPOSE: To allocate an i-node from the i-node array which is currently not allocated.

XII. void mkdirectory(char* filename, unsigned int newinode);
--------------------------------------------------------------------------------------
INPUT: 
1. Name of the directory.
2. i-node number allocated for the directory
RETURNS: NA
PURPOSE: 
1. To create a directory with the given name in the root directory. 
2. To check if the directory with the given name already exists if so return an appropriate message.
3. Fill the first two entries in its data block with "." and ".."

XIII. int directorywriter(fs_inode rootinode, dir dir);
--------------------------------------------------------------------------------------
INPUT: 
1. i-node reference of the parent directory, in this program it is always the root directory i-node reference.
2. Current directory reference.
RETURNS: "0" if the write to the first two entries of the allocated Data block is successful else "1" for a duplicate existing directory.
PURPOSE: To check if the directory with the given name already exists if so return an appropriate message.

XIV. int makeindirectblocks(int fd,int block_num);
--------------------------------------------------------------------------------------
INPUT: File descriptor of the external file in cpin, and the destination V6 file's Block Number 
RETURNS: "0" is returned for a successful allocation else "-1" for a file larger than the size supported by this file system. 
PURPOSE: Allocation of Indirect Blocks to the address elements of i-node. 

XV. void cpin(char* src, char* targ);
--------------------------------------------------------------------------------------
INPUT: source file path  (of external file) and destination filename (of V6 file)
RETURNS: NA
PURPOSE: 1. To copy the contents of the external file to the V6 file by allocating data blocks and an inode to the V6 file.
2. To write filename and inode number in its parent directory(in our case root directory).
3. To transfer control to function that makes indirect blocks if the file becomes large ( filesize > 56 KB)


XVI. void cpout(char* src, char* targ);
--------------------------------------------------------------------------------------
INPUT: source filename (V6 file) in the root directory and destination file path ( of external file)
RETURNS: NA
PURPOSE: 1. To copy the contents of the V6 file to the external file.
2. To read filename and inode number of the V6 file in its parent directory(in our case root directory).
3. To handle large files ( filesize > 56 KB) by reading block numbers in indirect blocks.


------------------------------------------------END -----------------------------------------------------------------------------------------------------------
================================================================================================================================================================
================================================================================================================================================================