Alternate Data Stream is an NTFS file attribute and was designed to provide compatibility with MacOS's Hierarchical file system.
- Any file created on the NTFS will have two data streams:
	- Data Stream : Default stream that contains the file data
	- Resource Stream : Typically contains the metadata of the file
- Attackers use ADS to hide malicious code or executable in the file attribute in order to evade detection
- This can be done by storing the malicious code or executable in the file attribute resource stream (metadata) of a legitimate file.