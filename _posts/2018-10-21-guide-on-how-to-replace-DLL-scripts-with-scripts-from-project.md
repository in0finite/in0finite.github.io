---
layout: post
title: Guide on how to replace DLL scripts with scripts from project
---

<br>

- backup project

- grab scripts from this repository: https://github.com/in0finite/uDLLUtils, and copy them to Editor folder

- make sure that asset serialization mode is set to text (restart Unity after you change it)

- open window (from menu: Window => MetaFileUtils), click "Refresh list of dlls", select your DLL, and save DLL script ids to file

- close Unity

- copy script files to project

- start Unity (it will generate meta files for scripts)

- You may now see some compilation errors. That could be because some types/method calls are ambigious. Now exclude DLL from compilation (select your DLL in project view, check "Editor" under "Exclude platforms" in inspector, and click Apply ).

- now the project should compile fine, and "Assembly-CSharp" DLL should be created which holds all added scripts

- open window, click "Choose source xml file with script ids", and select previously saved script ids file

- check or uncheck "Test only" depending on if you only want to test it or not

- replace script ids by clicking "Replace"

- Now the scripts should be replaced. Restart Unity so that it reloads all modified assets. Now you can delete your DLL from project.


<br>

## Notes

- if your DLL project required preprocessor defines, you need to add them to Unity project



