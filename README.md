MAFIA (Multiple Application Framework in GPU Architectures) -- Beta Version
-----------------------------------------------------------
MAFIA was developed for supporting multiple applications execution on GPUs by HPCL Group
at The Pennsylvania State University. Currently, it supports 25 benchmarks from various 
benchmark suites(CUDA, Parboil, SHOC and Rodinia).From these benchmarks, you can construct 
300 2-application workloads and 2300 3-application workloads.It was implemented by extending 
v3.2 GPGPU-Sim simulator.

If you use or build on this framework, please cite:


Adwait Jog, Onur Kayiran, Tuba Kesten, Ashutosh Pattnaik, Evgeny Bolotin, Niladrish Chatterjee, 
Stephen W. Keckler, Mahmut T. Kandemir, Chita R. Das, 
Anatomy of GPU Memory System for Multi-Application Execution, 
In the Proceedings of 1st International Symposium on Memory Systems (MEMSYS), Washington, DC, Oct 2015 


Setting up Environment 
----------------------
To enable working with MAFIA, first you need to setup GPGPU-Sim. The simulator can be found at
http://www.gpgpu-sim.org/

After downloading GPGPU-Sim, make sure you installed all dependencies required. You can take a look into GPGPU-Sim README file for the detailed information.

==Modify GPGPU-Sim For MAFIA Framework==

1. Replace src folder in GPGPU-Sim with MAFIA src folder. You can get clone from MAFIA Github
repository.
2. Build simulator by using the following commands (make sure that you change to bash shell) :

	cd v3.x
	source setup_environment
	make

3. Download benchmarks from MAFIA Github repository. Then, create subdirectory in ‘benchmarks’ folder(e.g. multiapp) and put files in that folder.
4. Change the directory to the folder you created. To compile and create executable file, the following will work:

	cd multiapp
	make
With these commands, the framework is ready to use. To clean the build, you can use ‘make clean’ command.

==Running benchmarks on MAFIA Framework==

1. You can see the available benchmarks in MAFIABENCHMARKS file.

2. Make sure, you have the following three files located in your benchmark folder.
	gpgpusim.config , gpuwattch_gtx480.xml, config_fermi_islip.icnt

3. Our framework has 4 modes.
	Single application with entire system : Executes single application by using entire SMs
	Single application: Executes single application based on number of allocated SMs
	2-application : Executes 2 application together
	3-application : Executes 3 application together

4. To run framework with one these modes, modify some parameters in gpgpusim.config.
	
	-gpgpu_n_clusters : indicates the number of SMs in the system.For 3-application mode, make sure that you choose a SMs number divisible by 3.
	-gpgpu_sms_app1 : indicates the number of SMs assigned to first application.
	-gpgpu_mode3 : indicates whether 3-application mode enabled or not.
	-gpu_app : indicates whether 2-application mode enabled or not.

Examples:

	2 application execution with even SM partitioning (15-15)
	-gpgpu_n_clusters 30
	-gpgpu_sms_app1 15
	-gpgpu_mode3 0
	-gpu_app 1

	2 application execution (12-24)
	-gpgpu_n_clusters 36
	-gpgpu_sms_app1 12
	-gpgpu_mode3 0
	-gpu_app 1
		
	3 application execution(10-10-10)
	-gpgpu_n_clusters 30
	-gpgpu_sms_app1 15
	-gpgpu_mode3 1
	-gpu_app 1

	single application execution (10)
	-gpgpu_n_clusters 30
	-gpgpu_sms_app1 10
	-gpgpu_mode3 0
	-gpu_app 0

If you modify the number of SMs, make sure you configure the interconnection network parameters in ‘config_fermi_islip.icnt’

5. After compilation, the executable file called ‘gpgpu_ptx_sim__mergedapps’ will be generated. To run, the following command will work:
	./gpgpu_ptx_sim__mergedapps <mode_name> <appnames>

<mode_name>

	Single application with entire system : -sing0
	Single application: -sing
	2-application : -apps
	3-application : -apps3
<appnames>
You can find abbreviations for benchmarks in MAFIABENCHMARKS file.

Examples:

Here some examples how to run benchmarks from our framework. BLK stands for Blackscholes benchmark, SCP stands for Scalar product benchmark and HISTO stands for Histogram benchmark. 

	Single application
	./gpgpu_ptx_sim__mergedapps -sing BLK

	Single application with entire system 
	./gpgpu_ptx_sim__mergedapps -sing0 BLK

	2-application
	./gpgpu_ptx_sim__mergedapps -apps BLK SCP

	3-application
	./gpgpu_ptx_sim__mergedapps -apps3 BLK SCP HISTO

==Results==

Our framework generates 3 output file (Stream1.txt, Stream2.txt and Stream3.txt) which represents the maximum number of benchmarks can be executed. Output files can be matched based on the argument order. For instance,

	./gpgpu_ptx_sim__mergedapps -apps BLK SCP

	Stream1.txt -> Output for BLK
	Stream2.txt -> Output for SCP
	Stream3.txt -> Empty
