# NodeOni
This IBM i library contains useful CL wrapper commands to allow Node.js apps to be called and consumed from regular IBM i jobs via CL, RPG or COBOL programs. This install assumes you have installed Node.js using the yum packages from IBM. Should work with any version of Node.js After calls are made to Node.js scripts, the caller can get return results (STDOUT) from the applications in one of the following formats: IFS file, OUTFILE, job log entries or spool file. 

The main benefit of this wrapper is to be able to integrate Node.js applications on-the-fly with standard IBM i job streams.

# Installing NODEONI library and creating NODE command objects

Download the **nodeoni.savf** save file from the selected releases page. 

https://github.com/richardschoen/NodeOni/releases

Upload the **nodeoni.savf** to the IFS and place it in **/tmp/nodeoni.savf**

Run the following commands to copy the save file from the IFS into a SAVF object

`CRTSAVF FILE(QGPL/NODEONI)`
 
`CPYFRMSTMF FROMSTMF('/tmp/nodeoni.savf') TOMBR('/qsys.lib/qgpl.lib/nodeoni.file') MBROPT(*REPLACE) CVTDTA(*NONE)`

Restore the NODEONI library

`RSTLIB SAVLIB(NODEONI) DEV(*SAVF) SAVF(QGPL/NODEONI)`

Build the NODEONI commands

`ADDLIBLE NODEONI`

`CRTCLPGM PGM(NODEONI/SRCBLDC) SRCFILE(NODEONI/SOURCE) SRCMBR(SRCBLDC) REPLACE(*YES)`

`CALL PGM(NODEONI/SRCBLDC)`

# Using the NODE CL command to call a Node.js application

The following example calls a Node.js app named: hello.js from the /nodeapps directory and passes the program a valid node command line via the NODE CL command. 

 ```
      NODE APPDIR('/nodeapps')          
      CMD('node hello.js')
      CCSID(37)
      DSPSTDOUT(*YES)    
      LOGSTDOUT(*NO)     
      PRTSTDOUT(*NO)     
      DLTSTDOUT(*YES)   
```      

# NODE command parms

**APPDIR** - The IFS directory location for the Node.js app. **Ex: /nodeapps**

**CMD** - Node.js command. **Ex: node hello.js**

**CCSID** - When using the iToolkit component for command access, I originally had some issues with CL commands not working correctly. However I don't currently remember exactly why. This may have been solved, however I recommend still passing a value of 37 unless you are in a non US country. If you set to *SAME, the CCSID will stay the same as your current job with no change.

**DSPSTDOUT** - Display the outfile contents. Nice when debugging. 

**LOGSTDOUT** - Place STDOUT log entries into the current jobs job log. Use this if you want the log info in the IBM i joblog. All STDOUT entries are written as CPF message: **NOD9898**

**PRTSTDOUT** - Print STDOUT to a spool file. Use this if you want a spool file of the log output.

**DLTSTDOUT** - This option insures that the STDOUT IFS temp files get cleaned up after processing. All IFS log files get created in the /tmp/mono directory.
