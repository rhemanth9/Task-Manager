import os
interval=5.0
from tkinter import *

Gui=Tk()
Gui.title("TASK MANAGER")

#--------------------------------CPU STATS--------------------------------

def CPU_UTLIZATION():
	global CPU
	CPU=Tk()
	CPU.title("CPU STATISTICS")
	global listcpu
	listcpu=Listbox(CPU,background="Green")
	listcpu.pack()
	listcpu.config(width=100,height=100)
	CPU_UTIL()
def CPU_UTIL():
		
	listcpu.delete(0,END)
	fo=open("/proc/stat","r")
	fr=fo.read()
	fs=fr.split()
	#listcpu.insert(END,(fs)
	cpucount=0
	for string in fs:
		if "cpu" in string:
			cpucount+=1
	#listcpu.insert(END,(cpucount))
	listcpu.insert(END,("Number of Cpu's: ",(cpucount-1)))
	i=0
	currumode=0
	currsmode=0
	curri=0
	while(i<(cpucount-1)):
		name = "cpu"+ str(cpucount-2)
		listcpu.insert(END,(name))
		indexcpu=fs.index(name)
		listcpu.insert(END,("--------------------------"))
		#listcpu.insert(END,(indexcpu))
		i+=1
		prevumode=currumode
		prevsmode=currsmode
		previ=curri
		currumode=float(fs[indexcpu+1])/100
		currsmode=float(fs[indexcpu+3])/100
		curri=float(fs[indexcpu+5])/100
		#listcpu.insert(END,(currumode))
		#listcpu.insert(END,(currsmode))
		#listcpu.insert(END,(curri))
		delu=currumode-prevumode
		dels=currsmode-prevsmode
		deli=curri-previ
		upercent=(delu)*100/(delu+dels+deli)
		spercent=(dels)*100/(delu+dels+deli)
		opercent=(delu+dels)*100/(delu+dels+deli)
		listcpu.insert(END,("USER mode Utlization",upercent))
		listcpu.insert(END,("SYSTEM mode Utlization",spercent))
		listcpu.insert(END,("OVERALL Utlization",opercent))

	#------------------------interrupts-----------------------------------

	currintr=0
	
	previntr=currintr
	currintr=fs[fs.index("intr")+1]
	#listcpu.insert(END,(currintr)
	intr=(int(currintr)-previntr)/interval
	listcpu.insert(END,("\n"))
	listcpu.insert(END,("Number of INTERRUPTS :",intr))


	#----------------------contextswitches----------------------------------


	currsw=0
	prevsw=currsw
	currsw=fs[fs.index("ctxt")+1]
	#listcpu.insert(END,(currintr))
	sw=(int(currsw)-prevsw)/interval
	listcpu.insert(END,("\n"))
	listcpu.insert(END,("Number of CONTEXT SWITCHES :",sw))


	#----------------------Memory utlization--------------------------------


	mo=open("/proc/meminfo","r")
	mr=mo.read()
	ms=mr.split()
	#listcpu.insert(END,(ms))
	availm=ms[ms.index("MemAvailable:")+1]
	totalm=ms[ms.index("MemTotal:")+1]
	memutil=int(availm)*100/int(totalm)
	listcpu.insert(END,("\n"))
	listcpu.insert(END,("AVAILABLE MEMORY	",availm))
	listcpu.insert(END,("TOTAL MEMORY	",totalm))
	listcpu.insert(END,("MEMORY UTILIZATION	",memutil))
	CPU.after(2000,CPU_UTIL)


#--------------------------DISK STATISTICS-----------------------------


def Disk_stats():
	global disk
	disk=Tk()
	disk.title("DISK STATISTICS")
	global listdisk
	listdisk=Listbox(disk,background="Orange")
	listdisk.pack()
	listdisk.config(width=100,height=100)
	disk_s()
def disk_s():	
	#--------------------------Disk Stats--------------------------------
	
	dro=0
	drn=0
	dwn=0
	dwo=0
	brn=0
	bro=0
	bwn=0
	bwo=0
	listdisk.delete(0,END)
	do=open("/proc/diskstats", "r")
	dr=do.read()
	ds=dr.split()
	#print(ds)
	dro=drn
	dwo=dwn
	bro=brn
	bwo=bwn
	drn=float(ds[ds.index("sda")+1])
	#print(diskread)
	brn=float(ds[ds.index("sda")+3])
	#print(brn)
	dwn=float(ds[ds.index("sda")+5])
	bwn=float(ds[ds.index("sda")+7])
	diskread=float((drn-dro)/2.0)
	blocksread=float((brn-bro)/2.0)
	diskwrites=float((dwn-dwo)/2.0)
	blockwrites=float((bwn-bwo)/2.0)

	listdisk.insert(END,("\n"))
	listdisk.insert(END,("Number of Disk READS		:",diskread))
	listdisk.insert(END,("Nuber of Blocks READ		:",blocksread))
	listdisk.insert(END,("Number of Disks Written		:",diskwrites))
	listdisk.insert(END,("Number of blocks written		:",blockwrites))

	disk.after(2000,disk_s)


#---------------------------NET STATS------------------------------------

def NET_stats():
	global net
	net=Tk()
	net.title("NET STATISTICS")
	global listnet
	listnet=Listbox(net,background="grey")
	listnet.pack()
	listnet.config(width=100,height=100)
	NET_UTIL()
def NET_UTIL():
		
	listnet.delete(0,END)

	#------------------------TCP Connections-----------------------------


	to=open("/proc/net/tcp","r")
	tr=to.read()
	ts=tr.split("\n")
	x=3
	count=0
	for i in range(1,(len(ts)-1)):
		#print(ts[i])
		ts1=ts[i].split()
		#print(ts1[1])
		#print(x)
		if(ts1[x]=="01"):
			count+=1
	listnet.insert(END,("\n"))
	listnet.insert(END,("TCP CONNECTIONS	:",(len(ts)-2)))
	listnet.insert(END,("\n"))
	listnet.insert(END,("ESTABLISHED TCP CONNECTIONS	:",count))
	listnet.insert(END,("\n"))


	#--------------------------TCP-------------------------------------

	to=open("/proc/net/tcp","r")
	tr=to.read()
	ts=tr.split("\n")
	
	for i in range(1,(len(ts)-1)):
		#print(ts[i])
		ts1=ts[i].split()
		src=ts1[1]
		dest=ts1[2]
		#print(src)
		s=src.split(":")
		#print(dest)
		d=dest.split(":")
		import socket
		import struct	
		addr1=socket.inet_ntoa(struct.pack("!L",int(s[0],16)))
		addr2=socket.inet_ntoa(struct.pack("!L",int(d[0],16)))
		#print(addr)
		
		listnet.insert(END,("----------TCP-------------"))
		try:
			h1,a,l=socket.gethostbyaddr(addr1)
			listnet.insert(END,("SOURCE		:",h1))
		except:
			listnet.insert(END,("SOURCE		:0:0:0:0"))
		try:
			h2,a,l=socket.gethostbyaddr(addr2)
			listnet.insert(END,("DESTINATION	:",h2))
		except:
			listnet.insert(END,("DESTINATION	:0:0:0:0"))
		inode=ts1[7]
		ino1=int(ts1[9])
		etc=open("/etc/passwd","r")
		etr=etc.read()
		e=etr.split(":")
		#print(e)
		x=e.index(inode)
		listnet.insert(END,("USER NAME	:",e[x-2]))
		
	
		prgo=os.listdir("/proc")
		#print(prgo)
		inde=int(prgo.index("1"))
		#print(inde)
		#print(ino1)
		
		for y in range(0,len(prgo)):
			try:
				prgo[y]=int(prgo[y])
				#print(prgo[y])
			except:
				a=0
		for ff in range(0,len(prgo)):
			try:
				x456=os.listdir("/proc/%d/fd"%(prgo[ff]))
				x123=os.popen("ls -l /proc/%d/fd"%(prgo[ff])).read()
				xs=x123.split("\n")
				
					#(xs)
				k=1
				while(k<len(xs)):
					try:
						ef=xs[k].split()
						k+=1
						soc=ef[10].split(":")
						socn=soc[1]
						socn=socn[socn.find("[")+1:socn.find("]")]
						if ino1==int(socn):
							try:
								x99= open("/proc/%s/comm"%prgo[ff],"r")
								xr99=x99.read()
								listnet.insert(END,("PROGRAM NAME	:",xr99))
								listnet.insert(END,("\n"))
							except:
								a=0
					except:
						f=0
			except:
				v=0

		

	#-----------------------------UDP--------------------------------------


	to=open("/proc/net/udp","r")
	tr=to.read()
	ts=tr.split("\n")
	for i in range(1,(len(ts)-1)):
		#print(ts[i])
		ts1=ts[i].split()
		src=ts1[1]
		dest=ts1[2]
		#print(src)
		s=src.split(":")
		#print(dest)
		d=dest.split(":")
		import socket
		import struct	
		addr1=socket.inet_ntoa(struct.pack("!L",int(s[0],16)))
		addr2=socket.inet_ntoa(struct.pack("!L",int(d[0],16)))
		#print(addr)
		
		listnet.insert(END,("----------UDP-------------"))
		try:
			h1,a,l=socket.gethostbyaddr(addr1)
			listnet.insert(END,("SOURCE		:",h1))
		except:
			listnet.insert(END,("SOURCE		:0:0:0:0"))
		try:
			h2,a,l=socket.gethostbyaddr(addr2)
			listnet.insert(END,("DESTINATION	:",h2))
		except:
			listnet.insert(END,("DESTINATION	:0:0:0:0"))
		inode=ts1[7]
		etc=open("/etc/passwd","r")
		etr=etc.read()
		e=etr.split(":")
		#print(e)
		x=e.index(inode)
		listnet.insert(END,("USER NAME	:",e[x-2]))
		ino2=ts1[9]
		ino2=int(ino2)
		#print(ino2)
		prgo=os.listdir("/proc")
		#print(prgo)
		inde=int(prgo.index("1"))
		#print(inde)
		#print(ino1)
		
		for y in range(0,len(prgo)):
			try:
				prgo[y]=int(prgo[y])
				#print(prgo[y])
			except:
				f=0
		for ff in range(0,len(prgo)):
			try:
				x456=os.listdir("/proc/%d/fd"%(prgo[ff]))
				x123=os.popen("ls -l /proc/%d/fd"%(prgo[ff])).read()
				xs=x123.split("\n")
				
					#(xs)
				k=1
				while(k<len(xs)):
					try:
						ef=xs[k].split()
						k+=1
						soc=ef[10].split(":")
						socn=soc[1]
						#print("KI")
						socn=socn[socn.find("[")+1:socn.find("]")]
						#print(socn)
						if ino2==int(socn):
							try:
								x99= open("/proc/%s/comm"%prgo[ff],"r")
								#print("HI")
								xr99=x99.read()
								listnet.insert(END,("PROGRAM NAME	:",xr99))
								listnet.insert(END,("\n"))
							except:
								a=0
					except:
						xy=0
			except:
				v=0




	#----------------------Network Utilization--------------------------


	nu=open("/proc/net/dev", "r")
	nr=nu.read()
	ns=nr.split(":")
	#print(ns)
	ns1=ns[1].split()
	#print(ns1[0])
	#print(ns1[8])
	total=int(ns1[0]+ns1[8])
	bo=os.popen("ethtool enp0s3| grep -i speed")
	br=bo.read()
	bs=br.split()
	#print(bs[1])
	x=bs[1]
	band=int(x[0:x.find("M")])
	#print(bs)
	NU=total/(band*10000000)
	listnet.insert(END,("Network Utlisation	:",NU))


	net.after(4000,NET_UTIL)



#-------------------------------PER PROCESS-----------------------------


def proc_stats():
	global pro
	pro=Tk()
	pro.title("PER PROCESS")
	global listpro
	listpro=Listbox(pro,background="pink")
	listpro.pack()
	listpro.config(width=100,height=100)
	pro_s()
def pro_s():
	listpro.delete(0,END)
	co=os.listdir("/proc")
	x=len(co)
	y=co.index('1')
	#print(co.index('1'))

	
	fo=open("/proc/stat","r")
	fr=fo.read()
	fs=fr.split()
	
	cpucount=0
	for string in fs:
		if "cpu" in string:
			cpucount+=1
	#listcpu.insert(END,(cpucount))
	#listcpu.insert(END,("Number of Cpu's: ",(cpucount-1)))
	i=0
	currumode=0
	currsmode=0
	curri=0
	while(i<(cpucount-1)):
		name = "cpu"+ str(cpucount-2)
		
		indexcpu=fs.index(name)
		i+=1
		prevumode=currumode
		prevsmode=currsmode
		previ=curri
		currumode=float(fs[indexcpu+1])/100
		currsmode=float(fs[indexcpu+3])/100
		curri=float(fs[indexcpu+5])/100
		tot=currumode+currsmode+curri
	for i in range(y,x):
		po=open("/proc/%s/stat"%co[i])
		pr=po.read()
		ps=pr.split()
		#print(ps)



		vm=float(ps[22])
		pm=float(ps[23])
		umod=float(ps[13])
		smod=float(ps[14])
		um=(umod/tot)*100
		sm=(smod/tot)*100
		total=um+sm
		#total=(total/tot)*100		
		#print(smod)
		#print(umod)
		#print(vm)
		#print(co[i])
		mo=open("/proc/meminfo","r")
		mr=mo.read()
		ms=mr.split()
		#print(ms)
		#availm=ms[ms.index("MemAvailable:")+1]
		#totalm=ms[ms.index("MemTotal:")+1]	
	
		tm=float(ms[ms.index("MemTotal:")+1])
		#print(tm)
		vm=(vm*100)/tm
		vm=vm/1028
		pm=(pm*100)/tm
		listpro.insert(END,("Process ID			:",co[i]))
		listpro.insert(END,("System mode Utilization	:",sm))
		listpro.insert(END,("User mode Utilization	:",um))
		listpro.insert(END,("overall Utilization	:",total))	
		listpro.insert(END,("VIRTUAL MEMORY 		:",vm))
		listpro.insert(END,("PHYSICAL MEMORY		:",pm))
		abc=ps[1].split("(")
		#print(abc)
		xyz=abc[1].split(")")
		listpro.insert(END,("PROGRAM NAME	:",xyz[0]))
		
		ppo=open("/proc/%s/status"%co[i])
		ppr=ppo.read()
		pps=ppr.split()
		#print(pps)
		ino=pps.index("Uid:")
		#print(ps[ino+1])
		inode=ps[ino+1]
		etc=open("/etc/passwd","r")
		etr=etc.read()
		e=etr.split(":")
		#print(e)
		try:
			x=e.index(inode)
			listpro.insert(END,("USER NAME		:",e[x-2]))
		except:	
				k=0
		listpro.insert(END,("\n"))


	pro.after(2000,pro_s)




	


w=Label(Gui,text="HEMANTH RACHAKONDA\n",font="italic")
w.pack()
b1=Button(Gui,text="CPU UTILIZATION",bg="Green",fg="black",width=40,command=CPU_UTLIZATION)
b1.pack()
b2=Button(Gui,text="DISK STATS",bg="orange",fg="black",width=40,command=Disk_stats)
b2.pack()
b3=Button(Gui,text="NETWORK STATS",bg="grey",fg="black",width=40,command=NET_stats)
b3.pack()
b4=Button(Gui,text="STATS FOR PER PROCESS",bg="pink",fg="black",width=40,command=proc_stats)
b4.pack()


Gui.mainloop()

