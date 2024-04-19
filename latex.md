## 补充《install-latex-guide-zh-cn.pdf》

## 装载texlive2022.iso  
右键单击texlive2023.iso文件 > “打开方式” > “windows资源管理器”

## 安装方式  
./install-tl-windows.bat --no-gui  
选择默认安装路径（C:\texlive\2023）输入： I  

## 升级宏包(可跳过)
使用GUI的方式升级，命令升级会频繁下载文件失败  
可用源：华为云，已测试其他源不通

## 配置vscode编译环境
安装插件：latex workshop  
打开一个.tex文件，按F1打开settings.json文件，增加以下设置
```
"latex-workshop.latex.tools": [
        {
            "name": "xelatex",
            "command": "xelatex",
            "args": [
                "-synctex=1",
                "-interaction=nonstopmode",
                "-file-line-error",
                "%DOC%"
            ]
        },
        {
            "name": "biber",
            "command": "biber",
            "args": [
                "%DOC%"
            ]
        }
    ],
    "latex-workshop.latex.recipes": [
        {
            "name": "xelatex*2",
            "tools": [
                "xelatex",
                "xelatex"
            ]
        },
        {
            "name": "xelatex -> biber -> xelatex*2",
            "tools": [
                "xelatex",
                "biber",
                "xelatex",
                "xelatex"
            ]
        },
        {
            "name": "xelatex",
            "tools": [
                "xelatex"
            ]
        }
    ],
    "latex-workshop.view.pdf.viewer": "tab",
    "latex-workshop.view.pdf.internal.synctex.keybinding": "double-click",
    "latex-workshop.latex.autoBuild.run": "never",
    "latex-workshop.latex.autoClean.run": "onBuilt",
    "latex-workshop.intellisense.citation.backend":"biblatex",
    "latex-workshop.latex.clean.method": "glob",
    "latex-workshop.latex.clean.subfolder.enabled": true,
    "latex-workshop.latex.clean.fileTypes": [
        "%DOCFILE%.aux",
        "%DOCFILE%.bbl",
        "%DOCFILE%.blg",
        "%DOCFILE%.idx",
        "%DOCFILE%.ind",
        "%DOCFILE%.lof",
        "%DOCFILE%.lot",
        "%DOCFILE%.out",
        "%DOCFILE%.toc",
        "%DOCFILE%.acn",
        "%DOCFILE%.acr",
        "%DOCFILE%.alg",
        "%DOCFILE%.glg",
        "%DOCFILE%.glo",
        "%DOCFILE%.gls",
        "%DOCFILE%.fls",
        "%DOCFILE%.log",
        "%DOCFILE%.fdb_latexmk",
        "%DOCFILE%.snm",
        "%DOCFILE%.synctex(busy)",
        "%DOCFILE%.synctex.gz(busy)",
        "%DOCFILE%.nav",
        "%DOCFILE%.vrb",
        "%DOCFILE%-[0-9].cpt",
        "%DOCFILE%.run.xml",
        "%DOCFILE%.bcf"
    ]
```
