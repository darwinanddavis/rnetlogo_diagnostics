# RNetLogo diagnostics

### Matthew Malishev<sup>1*</sup>

##### _<sup>1</sup> Department of Biology, Emory University, 1510 Clifton Road NE, Atlanta, GA, USA, 30322_  

##### *Corresponding author: matthew.malishev@gmail.com  

:link: [Github](https://github.com/darwinanddavis/rnetlogo_diagnostics)  

Versions:  
 - R 3.5.0  
 - RStudio 1.1.453  
 - `rJava` 0.9-10  
 - `RNetLogo` 1.0-4  
 - JGR 3.4.1  
 - Netlogo 6.0.4    

******

File extensions:   
.R  
.Rmd  
.nlogo     
.csv    
.pdf  
.rtf  
.html  

******  

### :pig: Troubleshooting steps for diagnosing Java, `rJava`, and `RNetLogo` errors when setting up NetLogo model in RStudio for Mac OSX (10+).     

First set Netlogo paths in `R`     
```{r}
# setup netlogo in r  
ver <-"6.0.4" # type in Netlogo version
nl.path <- "<netlogo path>" ; nl.path # set path to Netlogo program location
model.path <- "<netlogo model path>" # set path to Netlogo model    
nl.model <- "<netlogo model>" # netlogo model. file = .nlogo  

nl.path <- paste0(nl.path,"NetLogo ",ver,"/Java/"); nl.path
model.path <- paste0(,"/"); model.path   

```
  
:one: [Installing compiler toolchain for Mac OSX](https://thecoatlessprofessor.com/programming/r-compiler-tools-for-rcpp-on-macos/)    
:two: If rJava error persists, run the following in Terminal (sources: https://stackoverflow.com/questions/30738974/rjava-load-error-in-rstudio-r-after-upgrading-to-osx-yosemite and http://paulklemm.com/blog/2015-02-20-run-rjava-with-rstudio-under-osx-10-dot-10/):  
``` sudo ln -s $(/usr/libexec/java_home)/jre/lib/server/libjvm.dylib /usr/local/lib ```  
:three: Java troubleshooting. Run the following code in `R` (tests are sequential):    
```{r}
# java troubleshooting. tests are sequential. 
# https://stackoverflow.com/questions/14915898/rnetlogo-function-nlstart-fails-to-launch-gui

test.java <- 1 # 1 = run java diagnostics  

if(test.java==1){
  # test 1  
  # test java is working
  .jinit() 
  .jnew( "java/awt/Point", 10L, 10L )
  f <- .jnew("java/awt/Frame","Hello")
  .jcall(f,"setVisible",TRUE)
  
  # test 2
  component <- .jnull()
  component <- .jcast(component, new.class = "java/awt/Component")
  message <- .jnew("java/lang/String","This is a JOptionPane test from rJava.")
  message <- .jcast(message, new.class = "java/lang/Object")
  title <- .jnew("java/lang/String","Test")
  type <- .jnew("java/lang/Integer", as.integer(2))
  f <- .jnew("javax/swing/JOptionPane")
  .jcall(f,"showMessageDialog", component, message, title, .jsimplify(type))
  
  # test 3
  .jcall("java/lang/System", "S", "getProperty", "java.vm.version")
  .jcall("java/lang/System", "S", "getProperty", "java.vm.name")
  .jcall("java/lang/System", "S", "getProperty", "java.vm.info")
  .jcall("java/lang/System", "S", "getProperty", "java.runtime.version")
  .jcall("java/lang/System", "S", "getProperty", "sun.arch.data.model")
  
  # test 4
  .jcall("java/lang/System", "S", "getProperty", "java.awt.headless")
  Sys.getenv("NOAWT")
}

```  
:four: [GCC compiler in `R`__(unconfirmed)__](https://stackoverflow.com/questions/1616983/building-r-packages-using-alternate-gcc)  
:five: [Running Netlogo 6.0.+](https://github.com/NetLogo/NetLogo/issues/1282)  
:six: The following error in `R` when executing `NLStart()` means you have successfully navigated the `rJava` errors, but are running into a Java issue that's preventing `R` from generating the NetLogo GUI from `R`:  
```{r}
java.awt.HeadlessException
    at java.awt.GraphicsEnvironment.checkHeadless(GraphicsEnvironment.java:204)
    at java.awt.Window.<init>(Window.java:536)
    at java.awt.Frame.<init>(Frame.java:420)
    at java.awt.Frame.<init>(Frame.java:385)
    at javax.swing.SwingUtilities$SharedOwnerFrame.<init>(SwingUtilities.java:1758)
    at javax.swing.SwingUtilities.getSharedOwnerFrame(SwingUtilities.java:1833)
    at javax.swing.JOptionPane.getRootFrame(JOptionPane.java:1696)
    at javax.swing.JOptionPane.showOptionDialog(JOptionPane.java:863)
    at javax.swing.JOptionPane.showMessageDialog(JOptionPane.java:666)
    at javax.swing.JOptionPane.showMessageDialog(JOptionPane.java:637)
    at nlcon.NLink.<init>(NLink.java:109)  
```  

For the above error, the `NLStart()` function should run successfully with `gui=F`, which launches a headless GUI mode.     
`NLStart(nl.path,gui=F,nl.jarname = paste0("netlogo-",ver,".jar")) # open netlogo`  

:seven: If none of the above steps work in R/RStudio, you can run Netlogo from `JGR`, the Java version of `R`.   
   
In R/RStudio, run the following:  
```{r}  
install.packages('JGR',,'http://www.rforge.net/')  
library(JGR)  
JGR::JGR()# open JGR  
  ```  
  Then run the normal `NLLoad()` and `NLStart()` functions in JGR to launch Netlogo.  
  
### Attempted diagnostics:  
25-9-18    

1.     
```{r}
Sys.setenv(NOAWT=1)   
Sys.unsetenv("NOAWT") 
```  

2. Attempted with both NL v. 5.3.0 and 6.0.4  
```{r}
ver <-"6.0.4" # type in Netlogo version  
nl.path <- "/Users/malishev/Documents/Melbourne Uni/Programs/" ; nl.path # set path to Netlogo program location

nl.path <- paste0(nl.path,"NetLogo ",ver,"/"); nl.path # opt 1
nl.path <- paste0(nl.path,"NetLogo ",ver,"/Java/"); nl.path # opt 2 
nl.path <- paste0(nl.path,"NetLogo ",ver,"/Java"); nl.path # opt 3 

model.path <- paste0(wd,"/"); model.path # set path to Netlogo model  
nl.model <- "DEB_INF_IBM_almost_working2.nlogo" # name of Netlogo model
NLStart(nl.path,nl.jarname = paste0("netlogo-",ver,".jar")) # open netlogo
```  

## References  
- [Compiler toolchain for Mac OSX](https://thecoatlessprofessor.com/programming/r-compiler-tools-for-rcpp-on-macos/)  
- Diagnosing `rjava` error: [link 1](https://stackoverflow.com/questions/30738974/rjava-load-error-in-rstudio-r-after-upgrading-to-osx-yosemite) and [link 2](http://paulklemm.com/blog/2015-02-20-run-rjava-with-rstudio-under-osx-10-dot-10/)  
- [Running RNetlogo in JGR](https://groups.yahoo.com/neo/groups/netlogo-users/conversations/topics/14817)  
- [2018 Git post summarising current troubleshooting notes](https://github.com/NetLogo/NetLogo/issues/1282)  
- [Java troubleshooting](https://stackoverflow.com/questions/14915898/rnetlogo-function-nlstart-fails-to-launch-gui)  

