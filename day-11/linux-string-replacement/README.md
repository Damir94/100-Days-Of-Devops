# Replace String in XML Template File — Maintenance Task Documentation
## Overview
As part of routine maintenance at xFusionCorp Industries, the system administration team updates template XML files on the Stratos DC backup server. These XML templates are used by the Nautilus application and need valid data substitutions before use.

In this task, we replaced all occurrences of the string Text with LUSV in the file:
  - /root/nautilus.xml

## Objective
Update the XML template by replacing placeholder values with required data.

Task:
  - Substitute all occurrences of Text with LUSV in /root/nautilus.xml.

## Steps Performed
1 Connect to Backup Server
Access the backup server using SSH:
```bash
ssh root@<backup-server-ip>
```
2. Verify the XML File Exists
```bash
ls -l /root/nautilus.xml
```
3. Perform Global String Replacement
Use the sed stream editor to replace every instance of Text:
```bash
sed -i 's/Text/LUSV/g' /root/nautilus.xml
```
Explanation:
| Flag      | Meaning                                       |
| --------- | --------------------------------------------- |
| `-i`      | Edit file in place                            |
| `s/a/b/g` | Substitute `a` with `b` globally in each line |


4. Verify Changes
```bash
grep LUSV /root/nautilus.xml
```
or view full file:
```bash
cat /root/nautilus.xml
```
### Before & After
| Before                        | After                         |
| ----------------------------- | ----------------------------- |
| `<data>Text</data>`           | `<data>LUSV</data>`           |
| `<info>Text goes here</info>` | `<info>LUSV goes here</info>` |

### Best Practices
Always take a backup before modifying configuration/system files:
```bash
cp /root/nautilus.xml /root/nautilus.xml.bak
```
Verify results after running the replacement command.
### Troubleshooting
| Issue                  | Fix                                         |
| ---------------------- | ------------------------------------------- |
| `Permission denied`    | Use root or `sudo` as needed                |
| File not found         | Confirm you're on the correct server & path |
| Incorrect replacements | Restore from `.bak` and rerun command       |
