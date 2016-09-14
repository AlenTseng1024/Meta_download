# Meta_download
The purpose of this python scrip is going to capture meta build files
PROCESS_DEBUG = False
PROCESS_DRY_RUN = False
import sys
import re
import pdb
import os
import getopt

count = 0
#vdesk_folder = "D:\yenpint\\"
#destination_folder = "\\\yenpint-linux\workspace2"

meta_tag_array = ['Build Location', 'ADSP',      	'BOOT',         'CNSS',      	'CPE',      	'LA',    	'MPSS',       	'RPM',      'TZ'              	]
meta_folder_array = ['common',            'adsp_proc', 	'boot_images',  'wcnss_proc', 	'cpe_proc', 	'LINUX', 	'modem_proc', 	'rpm_proc', 'trustzone_images'	]
meta_folder_copy_rule = ['all',     'all',       	'all',          'folder_only',	'folder_only', 	'image_only','folder_only', 'all',		'all'				]
meta_folder_location_array = ['',   '',				'',            	'',          	'',          	'',      	'',          	'',          '']

meta_copy_rule = ['folder_only', 'image_only', 'source_code_only', 'all', 'user_define']

def enum(**enums):
    return type('Enum', (), enums)	
#meta enumeration
m_e = enum(COMMON = 0,	ADSP = 1,	BOOT = 2,	CNSS = 3,	CPT = 4,	LA = 5,	MPSS = 6,	RPM = 7,	TZ = 8)
#type enumeration
t_e = enum(TAG = 0, 	FOLDER_NAME = 1,	COPY_RULE = 2,	LOCATION = 3)

""" meta_files_download(meta_tag, folder_location):
Step 2.2.1 Download meta data one by one
"""
def meta_files_download(meta_folder, meta_folder_location, meta_index):
	#Copy all
	if meta_folder_copy_rule[meta_index].find("all")>=0:
		command = "xcopy \\" + meta_folder_location + "\\" + meta_folder_array[meta_index] + "\* " + vdesk_folder + build_id.rstrip() + "\\" + meta_folder_array[meta_index] + "\\ /s /Q"
		if PROCESS_DRY_RUN == True : 
			print command
		else:
			os.system(command)
	#Copy empty folder only
	elif meta_folder_copy_rule[meta_index].find("folder_only")>=0:
		command = "xcopy \\" + meta_folder_location + "\\" + meta_folder_array[meta_index] + "\* " + vdesk_folder + build_id.rstrip() + "\\" + meta_folder_array[meta_index] + "\\ /s /e /h /T /Q"
		if PROCESS_DRY_RUN == True : 
			print command
		else:
			os.system(command)		
	#Copy build image
	elif meta_folder_copy_rule[meta_index].find("image_only")>=0:
		if meta_folder.find("LINUX")>=0 :
			source_linux_path = meta_folder_location + "\\LINUX\\android\\out\\target\\product\\msm" + linux_tag + "\\"
			destination_linux_path = vdesk_folder + build_id.rstrip() + "\\LINUX\\android\\out\\target\\product\\msm" + linux_tag + "\\"
			command = "copy \\" + source_linux_path + "boot.img " + destination_linux_path
			if PROCESS_DRY_RUN == True : 
				print command
			else:
				os.system(command)
			
			command = "copy \\" + source_linux_path + "cache.img " + destination_linux_path
			if PROCESS_DRY_RUN == True : 
				print command
			else:
				os.system(command)
			
			command = "copy \\" + source_linux_path + "emmc_appsboot.mbn " + destination_linux_path
			if PROCESS_DRY_RUN == True : 
				print command
			else:
				os.system(command)
			
			command = "copy \\" + source_linux_path + "persist.img " + destination_linux_path
			if PROCESS_DRY_RUN == True : 
				print command
			else:
				os.system(command)
			
			command = "copy \\" + source_linux_path + "recovery.img " + destination_linux_path
			if PROCESS_DRY_RUN == True : 
				print command
			else:
				os.system(command)
			
			command = "copy \\" + source_linux_path + "system.img " + destination_linux_path
			if PROCESS_DRY_RUN == True : 
				print command
			else:
				os.system(command)
			
			command = "copy \\" + source_linux_path + "userdata.img " + destination_linux_path
			if PROCESS_DRY_RUN == True : 
				print command
			else:
				os.system(command)
			
			command = "copy \\" + source_linux_path + "abl.elf " + destination_linux_path
			if PROCESS_DRY_RUN == True : 
				print command
			else:
				os.system(command)
			
			command = "copy \\" + source_linux_path + "secondary-boot\\" + "boot.img " + destination_linux_path + "secondary-boot\\"
			if PROCESS_DRY_RUN == True : 
				print command
			else:
				os.system(command)
			
			command = "copy \\" + source_linux_path + "secondary-boot\\" + "system.img " + destination_linux_path + "secondary-boot\\"
			if PROCESS_DRY_RUN == True : 
				print command
			else:
				os.system(command)
		else:
			print "On-going"


""" get_lnx_platform_tag(build_id):
Step 2.1.1 get linux tag
"""
def get_lnx_platform_tag(build_id):
	if build_id.find("MSM8996")>=0:
		return "cobalt";
	elif build_id.find("MSM8952")>=0:
		return "8952_64"
	else:
		print "UNSUPPORT LINUX TAG, check get_lnx_platform_tag() function!!!"

""" download_meta_files(meta_tag_array, meta_folder_location_array):
Step 2.2 Download meta data one by one
"""
def download_meta_files(meta_folder_array, meta_folder_location_array):
	print "Step2.2 : download meta files"
	for meta_index, meta_folder in enumerate(meta_folder_array):
		#Empty check, not empty string is allowed
		if bool(meta_folder) & bool(meta_folder_location_array[meta_index]):
			if(PROCESS_DEBUG == True):
				print "%s	:	%s" %(meta_folder, meta_folder_location_array[meta_index])
			meta_files_download(meta_folder, meta_folder_location_array[meta_index], meta_index)

""" initialize_folder(meta_tag, folder_location):
Step 2.1 Initialize folder
"""
def initialize_folder(build_id):
	global linux_tag
	print "Step2.1 : Initialize folder"
	#pdb.set_trace()
	linux_tag = get_lnx_platform_tag(build_id)
	command = "rmdir /s /q " + vdesk_folder + build_id.rstrip()
	if PROCESS_DRY_RUN == True : 
		print command
	else:
		os.system(command)
	command = "md " + vdesk_folder + build_id.rstrip()
	if PROCESS_DRY_RUN == True : 
		print command
	else:
		os.system(command)
	command = "md " + vdesk_folder + build_id.rstrip() + "\\LINUX\\android\\out\\target\\product\\msm" + linux_tag + "\\"
	if PROCESS_DRY_RUN == True : 
		print command
	else:
		os.system(command)
	command = "md " + vdesk_folder + build_id.rstrip() + "\\LINUX\\android\\out\\target\\product\\msm" + linux_tag + "\\secondary-boot\\"
	if PROCESS_DRY_RUN == True : 
		print command
	else:
		os.system(command)

""" dispatch_folder_location(meta_tag, folder_location):
Step 1.2.3 Dispatch folder location to each array
"""
def dispatch_folder_location(meta_tag, folder_location):
	row_number = 0
	for meta_tag_i in meta_tag_array:
		if meta_tag.find(meta_tag_i)>=0: #Found match mata tag, store folder_location to related location
			meta_folder_location_array[row_number] = folder_location
			break;
		row_number = row_number + 1

""" get_other_meta_information(meta_tag, content)
Step 1.2.2 Extract the other meta information
Extract pattern like
      ADSP.8952.2.6	ADSP.8952.2.6-00202-00000-1	\\chronicle\zipbuild178\PROD\ADSP.8952.2.6-00202-00000-1	Completed	Completed	SanDiego
"""
def get_other_meta_information(meta_tag, content):
	start_point = content.find(pattern_double_anti_slash)
	end_point = content.find(pattern_completed)
	folder_location = content[start_point+1:end_point-1]
	return folder_location

""" get_common_meta_information(meta_tag, content)
Step 1.2.1 Extract common folder meta information
Extract pattern like
      Build Location	\\chronicle\zipbuild190\PROD\MSM8952.LA.1.0.1-00110-STD.PROD-1
"""	
def get_common_meta_information(meta_tag, content):
	tag_pattern = content.find(meta_tag)
	folder_location = content[tag_pattern+len(meta_tag_array[m_e.COMMON])+2: len(content)-1]
	return folder_location

pattern_double_anti_slash = "\\"
pattern_completed = "Completed"
""" get_meta_information(content)
Step1.2 Get meta build information			get_meta_information(content)
Search pattern like
	ADSP.8952.2.6	ADSP.8952.2.6-00202-00000-1	\\chronicle\zipbuild178\PROD\ADSP.8952.2.6-00202-00000-1	Completed	Completed	SanDiego
	BOOT.BF.3.2.1	BOOT.BF.3.2.1-00023-M8952SAAAANAZ-1	\\bigelow\zipbuild268\PROD\BOOT.BF.3.2.1-00023-M8952SAAAANAZ-1	Completed	Completed	SanDiego
	CNSS.PR.3.0.c1	CNSS.PR.3.0.c1-00050-M8952BAAAANAZW-1	\\chronicle\zipbuild227\PROD\CNSS.PR.3.0.c1-00050-M8952BAAAANAZW-1	Completed	Completed	SanDiego
	CPE.TTF.2.0	CPE.TTF.2.0-00018-W9330AAAAAAAZQ-1	\\chronicle\zipbuild185\PROD\CPE.TTF.2.0-00018-W9330AAAAAAAZQ-1	Completed		SanDiego
	LA.BR.1.3.3	LA.BR.1.3.3-05910-8952.0-1	\\chronicle\zipbuild185\PROD\LA.BR.1.3.3-05910-8952.0-1	Completed	Completed	SanDiego
	MPSS.TA.1.0.c3	MPSS.TA.1.0.c3-00079-8952_GEN_PACK-1	\\chronicle\zipbuild168\PROD\MPSS.TA.1.0.c3-00079-8952_GEN_PACK-1	Completed		SanDiego
	RPM.BF.2.2.c2	RPM.BF.2.2.c2-00022-M8952AAAAANAZR-1	\\chronicle\zipbuild172\PROD\RPM.BF.2.2.c2-00022-M8952AAAAANAZR-1	Completed	Completed	SanDiego
	TZ.BF.3.0.3	TZ.BF.3.0.3-00034-M8952AAAAANAZT-1	\\chronicle\zipbuild218\PROD\TZ.BF.3.0.3-00034-M8952AAAAANAZT-1	Completed	Completed	SanDiego	  
"""
def get_meta_information(content):
	"""
	meta_tag = Build Location or ADSP...
	#content = Build Location	\\chronicle\zipbuild190\PROD\MSM8952.LA.1.0.1-00110-STD.PROD-1
	#ADSP.8952.2.6	ADSP.8952.2.6-00202-00000-1	\\chronicle\zipbuild178\PROD\ADSP.8952.2.6-00202-00000-1	Completed	Completed	SanDiego
	There are two types of meta folder, one is common without meta tag in the beginning and the others are all have it in the beginning.
	Same are all have "\\" to identify folder location
	"""
	global 	pattern_double_anti_slash
	global pattern_completed
	if content.find(pattern_double_anti_slash) >= 0: #Available file path, found "\\" pattern
		for meta_tag in meta_tag_array:
			if content.find(meta_tag) >=0: #Meta tag found!!!
				#Special case for COMMON folder only
				if meta_tag.find(meta_tag_array[m_e.COMMON]) >=0: 
					folder_location = get_common_meta_information(meta_tag, content)
				#Common case for meta folder
				else: 
					folder_location = get_other_meta_information(meta_tag, content)
				dispatch_folder_location(meta_tag, folder_location)
				break

""" get_build_id_information
Step1.1 Get Build ID information, 			get_build_id_information(content)
Search pattern like
      Build ID	MSM8952.LA.1.0.1-00110-STD.PROD-1
"""
build_id_tag = "Build ID"
build_id_found_flag = False
def get_build_id_information(content):
	global build_id
	global build_id_found_flag
	if content.find(build_id_tag) >=0 : #found Build ID
		start_point = content.find(build_id_tag)
		end_point = len(content)
		build_id = content[start_point+len(build_id_tag)+1 : end_point]
		build_id_found_flag = True
	if(PROCESS_DEBUG == True):
		print "Build ID : %s" %(build_id)

""" get_build_information(content)
Step1 : Get all the information from text file, get_build_information(content)
	Step1.1 Get Build ID information, 			get_build_id_information(content)
	Step1.2 Get meta build information			get_meta_information(content)
"""
def get_build_information(content):
	if build_id_found_flag == False:
		get_build_id_information(content)
	else:
		get_meta_information(content)
	
""" download_meta_information()
Step2 : Download the file/image					download_meta_information()
	Step2.1 Initialize/create the folder		initialize_folder(build_id)
	Step2.2	Donwload the files					download_meta_files(meta_folder_array, meta_folder_location_array)
"""
def download_meta_information():
	print "Step2 : downloading the meta information"
	initialize_folder(build_id)
	download_meta_files(meta_folder_array, meta_folder_location_array)

""" zip_files()
#Step3 : Zip files
"""
def zip_files():
	print "Step3 : Compressing Files..."
	command = "cd " + vdesk_folder
	if PROCESS_DRY_RUN == True : 
		print command
	else:
		os.system(command)
	command = vdesk_folder + "7-Zip\\7z.exe a " + vdesk_folder + build_id.rstrip() + ".zip " + vdesk_folder + build_id.rstrip()
	#command = "compress /c /s /q" + vdesk_folder + build_id.rstrip()
	if PROCESS_DRY_RUN == True : 
		print command
	else:
		os.system(command)
	print "Compressing Files done"
""" move_files()
#Step4 : Move files
"""
def move_files(destination):
	print "Step4 : Moving zipped Files to local destination..."
	command = "move " + vdesk_folder + build_id.rstrip() + ".zip " + destination
	if PROCESS_DRY_RUN == True : 
		print command
	else:
		os.system(command)
	print "Moving zipped Files to local destination done"

#Step0 : Get vdesk location and destination folder location from input argument.
def get_input_argument_information():
	global vdesk_folder
	global destination_folder
	global file_path
	try:
		options, arguments = getopt.getopt(sys.argv[1:], "v:d:f:", ["vdesk_folder=", "destination_folder=", "find_it_description="])
	except getopt.GetoptError:
		print "Get error, exit"
		sys.exit(2)
	for option, argument in options:
		#pdb.set_trace()
		if option in ("-v", "--vdesk_folder"):
			print 'vdesk_folder is ', argument
			vdesk_folder = argument
		elif option in ("-d", "--destination_folder"):
			print 'destination_folder is ', argument
			destination_folder = argument
		elif option in ("-f", "--find_it_description"):
			print 'find_it_description is ', argument
			file_path = argument
	
"""
Step0 : Get vdesk location and destination folder location from input argument.
	
Step1 : Get all the information from text file, get_build_information(file_content)
	Step1.1 Get Build ID information, 			get_build_id_information(content)
	Step1.2 Get meta build information			get_meta_information(content)
Step2 : Download the file/image					download_meta_information()
	Step2.1 Initialize/create the folder		initialize_folder(build_id)
	Step2.2	Donwload the files					download_meta_files(meta_folder_array, meta_folder_location_array)
Step3 : Zip files								zip_files(file_location)
Step4 : Move files								move_files(destination)
"""
#file_path = sys.argv[1]
line_number = 0;

#Step0 : Get vdesk location and destination folder location from input argument.
print"Step0 : Get vdesk location and destination folder location from input argument."
get_input_argument_information()

#Step1 : Get all the information from text file
print "Step1 : Get all the information from text file"
with open(file_path, "r") as file_handler:
	for file_content in file_handler:
		if (PROCESS_DEBUG == True):
			line_number = line_number + 1
			print line_number
		get_build_information(file_content)
	#1 check point of get_build_information()
	if (PROCESS_DEBUG == True):
		for index, meta_tag_debug in enumerate(meta_tag_array):
			print "%s	:	%s	:	%s" %(meta_tag_debug, meta_folder_array[index], meta_folder_location_array[index])
		#pdb.set_trace()
	
#Step2 : Download the file/image
download_meta_information()

#Step3 : Zip files
zip_files()

#Step4 : Move files
move_files(destination_folder)
