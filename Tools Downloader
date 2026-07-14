#Requires -RunAsAdministrator

$ErrorActionPreference = 'SilentlyContinue'

Add-Type -AssemblyName System.Windows.Forms
Add-Type -AssemblyName System.Drawing
Add-Type -AssemblyName System.IO.Compression.FileSystem

# ==============================================================================
# 1. SETUP CARTELLA E ANTIVIRUS
# ==============================================================================
$root = "C:\"
$name = "SignalX_Tools"
$i = 1
while (Test-Path "$root$name$i") { $i++ }
$script:folder = "$root$name$i"
[void](New-Item -Path $script:folder -ItemType Directory -Force)
Set-Location $script:folder

function Add-DefenderExclusion {
    try {
        if (Get-Command Get-MpPreference -ErrorAction SilentlyContinue) {
            $existing = (Get-MpPreference -ErrorAction SilentlyContinue).ExclusionPath
            if ($null -eq $existing -or $existing -notcontains $script:folder) {
                Add-MpPreference -ExclusionPath $script:folder -ErrorAction SilentlyContinue | Out-Null
            }
        }
    } catch {
        try {
            $regPath = "HKLM:\SOFTWARE\Microsoft\Windows Defender\Exclusions\Paths"
            if (Test-Path $regPath) {
                [void](New-ItemProperty -Path $regPath -Name $script:folder -Value 0 -PropertyType DWORD -Force -ErrorAction SilentlyContinue)
            }
        } catch { }
    }
}

$avPrompt = [System.Windows.Forms.MessageBox]::Show(
    "Vuoi aggiungere l'esclusione a Windows Defender per la cartella di download? (Raccomandato)",
    "Signal X - Antivirus Options",
    [System.Windows.Forms.MessageBoxButtons]::YesNo,
    [System.Windows.Forms.MessageBoxIcon]::Question
)
if ($avPrompt -eq "Yes") { Add-DefenderExclusion }

# ==============================================================================
# 2. DIZIONARIO DEI TOOL (Nome -> URL)
# ==============================================================================
$script:toolCategories = [ordered]@{
    "Zimmerman Tools" = [ordered]@{
        "TimelineExplorer"      = "https://download.ericzimmermanstools.com/net9/TimelineExplorer.zip"
        "JumpListExplorer"      = "https://download.ericzimmermanstools.com/net9/JumpListExplorer.zip"
        "ShellBagsExplorer"     = "https://download.ericzimmermanstools.com/net9/ShellBagsExplorer.zip"
        "RegistryExplorer"      = "https://download.ericzimmermanstools.com/net9/RegistryExplorer.zip"
        "PECmd"                 = "https://download.ericzimmermanstools.com/net9/PECmd.zip"
        "MFTECmd"               = "https://download.ericzimmermanstools.com/net9/MFTECmd.zip"
        "JLECmd"                = "https://download.ericzimmermanstools.com/net9/JLECmd.zip"
        "SrumECmd"              = "https://download.ericzimmermanstools.com/net9/SrumECmd.zip"
        "bstrings"              = "https://download.ericzimmermanstools.com/net9/bstrings.zip"
        "RecentFileCacheParser" = "https://download.ericzimmermanstools.com/net9/RecentFileCacheParser.zip"
    }
    "Nirsoft Tools" = [ordered]@{
        "WinPrefetchView"       = "https://www.nirsoft.net/utils/winprefetchview-x64.zip"
        "LastActivityView"      = "https://www.nirsoft.net/utils/lastactivityview.zip"
        "ExecutedProgramsList"  = "https://www.nirsoft.net/utils/executedprogramslist.zip"
        "UserAssistView"        = "https://www.nirsoft.net/utils/userassistview.zip"
        "AlternateStreamView"   = "https://www.nirsoft.net/utils/alternatestreamview-x64.zip"
        "HashMyFiles"           = "https://www.nirsoft.net/utils/hashmyfiles-x64.zip"
        "JumpListsView"         = "https://www.nirsoft.net/utils/jumplistsview.zip"
        "OpenSaveFilesView"     = "https://www.nirsoft.net/utils/opensavefilesview-x64.zip"
        "USBDeview"             = "https://www.nirsoft.net/utils/usbdeview-x64.zip"
        "TurnedOnTimesView"     = "https://www.nirsoft.net/utils/turnedontimesview.zip"
        "RegScanner"            = "https://www.nirsoft.net/utils/regscanner-x64.zip"
        "BrowserDownloadsView"  = "https://www.nirsoft.net/utils/browserdownloadsview-x64.zip"
        "Clipboardic"           = "https://www.nirsoft.net/utils/clipboardic.zip"
        "DriverView"            = "https://www.nirsoft.net/utils/driverview-x64.zip"
        "FileAccessErrorView"   = "https://www.nirsoft.net/utils/fileaccesserrorview-x64.zip"
        "PreviousFilesRecovery" = "https://www.nirsoft.net/utils/previousfilesrecovery-x64.zip"
        "RecentFilesView"       = "https://www.nirsoft.net/utils/recentfilesview.zip"
        "ShellBagsView"         = "https://www.nirsoft.net/utils/shellbagsview.zip"
        "TaskSchedulerView"     = "https://www.nirsoft.net/utils/taskschedulerview-x64.zip"
        "UninstallView"         = "https://www.nirsoft.net/utils/uninstallview-x64.zip"
        "USBDriveLog"           = "https://www.nirsoft.net/utils/usbdrivelog.zip"
        "FullEventLogView"      = "https://www.nirsoft.net/utils/fulleventlogview.zip"
        "ProduKey"              = "https://www.nirsoft.net/utils/produkey.zip"
        "NetworkTrafficView"    = "https://www.nirsoft.net/utils/networktrafficview.zip"
        "MyEventViewer"         = "https://www.nirsoft.net/utils/myeventviewer.zip"
        "WifiInfoView"          = "https://www.nirsoft.net/utils/wifiinfoview.zip"
        "IPNetInfo"             = "https://www.nirsoft.net/utils/ipnetinfo.zip"
    }
    "Spok's Tools" = [ordered]@{
        "JournalTrace"           = "https://github.com/spokwn/JournalTrace/releases/latest/download/JournalTrace.exe"
        "PathsParser"            = "https://github.com/spokwn/PathsParser/releases/latest/download/PathsParser.exe"
        "BAM-parser"             = "https://github.com/spokwn/BAM-parser/releases/latest/download/BAMParser.exe"
        "PrefetchParser"         = "https://github.com/spokwn/prefetch-parser/releases/latest/download/PrefetchParser.exe"
        "PcaSvcExecuted"         = "https://github.com/spokwn/pcasvc-executed/releases/download/v0.8.7/PcaSvcExecuted.exe"
        "ActivitiesCacheParser"  = "https://github.com/spokwn/ActivitiesCache-execution/releases/download/v0.6.5/ActivitiesCacheParser.exe"
        "Replaceparser"          = "https://github.com/spokwn/Replaceparser/releases/latest/download/Replaceparser.exe"
        "BamDeletedKeys"         = "https://github.com/spokwn/BamDeletedKeys/releases/latest/download/BamDeletedKeys.exe"
        "espouken"               = "https://github.com/spokwn/Tool/releases/latest/download/espouken.exe"
    }
    "Other Tools" = [ordered]@{
        "SystemInformer (Canary)" = "https://github.com/winsiderss/si-builds/releases/download/3.2.25275.112/systeminformer-build-canary-setup.exe"
        "Everything Search"       = "https://www.voidtools.com/Everything-1.4.1.1029.x64-Setup.exe"
        "FTK Imager"              = "https://d1kpmuwb7gvu1i.cloudfront.net/AccessData_FTK_Imager_4.7.1.exe"
        "CCleaner"                = "https://download.ccleaner.com/rcsetup154.exe"
        "DIE-engine"              = "https://github.com/horsicq/DIE-engine/releases/download/3.10/die_win64_portable_3.10_x64.zip"
        "HxD Portable"            = "https://mh-nexus.de/downloads/HxDPortableSetup.zip"
        "PEStudio"                = "https://www.winitor.com/tools/pestudio/current/pestudio.zip"
        "Sysinternals Strings"    = "https://download.sysinternals.com/files/Strings.zip"
        "Luyten"                  = "https://github.com/deathmarine/Luyten/releases/download/v0.5.4_Rebuilt_with_Latest_depenencies/luyten-0.5.4.jar"
        "Recaf"                   = "https://github.com/Col-E/Recaf/releases/download/2.21.14/recaf-2.21.14-J8-jar-with-dependencies.jar"
        "Process Explorer"        = "https://download.sysinternals.com/files/ProcessExplorer.zip"
        "Autoruns"                = "https://download.sysinternals.com/files/Autoruns.zip"
        "Process Monitor"         = "https://download.sysinternals.com/files/ProcessMonitor.zip"
        "TCPView"                 = "https://download.sysinternals.com/files/TCPView.zip"
        "Hayabusa"                = "https://github.com/Yamato-Security/hayabusa/releases/download/v3.7.0/hayabusa-3.7.0-win-aarch64.zip"
        "RAMMap"                  = "https://download.sysinternals.com/files/RAMMap.zip"
        "VMMap"                   = "https://download.sysinternals.com/files/VMMap.zip"
        "PSTools"                 = "https://download.sysinternals.com/files/PSTools.zip"
        "SDelete"                 = "https://download.sysinternals.com/files/SDelete.zip"
        "LogonSessions"           = "https://download.sysinternals.com/files/LogonSessions.zip"
        "ListDLLs"                = "https://download.sysinternals.com/files/ListDlls.zip"
        "WinObj"                  = "https://download.sysinternals.com/files/WinObj.zip"
        "AccessChk"               = "https://download.sysinternals.com/files/AccessChk.zip"
        "SigCheck"                = "https://download.sysinternals.com/files/Sigcheck.zip"
        "Disk2vhd"                = "https://download.sysinternals.com/files/Disk2vhd.zip"
    }
    "Memory Forensics & Python" = [ordered]@{
        "Volatility 3 v2.28.0 (source)"      = "https://github.com/volatilityfoundation/volatility3/archive/refs/tags/v2.28.0.zip"
        "Volatility 2.6 (legacy standalone)" = "https://github.com/volatilityfoundation/volatility/releases/download/2.6/volatility_2.6_win64_standalone.zip"
        "Magnet RAM Capture"                 = "https://s3.amazonaws.com/cdn.magnetforensics.com/Content/Files/MagnetRAMCapture/MagnetRAMCapture.exe"
        "Python 3.14.6 (x64, ultima)"        = "https://www.python.org/ftp/python/3.14.6/python-3.14.6-amd64.exe"
        "Python 3.13.14 (x64, LTS-friendly)" = "https://www.python.org/ftp/python/3.13.14/python-3.13.14-amd64.exe"
        "YARA v4.5.5 (win64)"                = "https://github.com/VirusTotal/yara/releases/download/v4.5.5/yara-4.5.5-2368-win64.zip"
        "ExifTool 13.59 (win64)"             = "https://downloads.sourceforge.net/project/exiftool/exiftool-13.59_64.zip"
    }
}

# stato "checked" persistente per ogni tool
$script:checkState = @{}
foreach ($cat in $script:toolCategories.Keys) {
    foreach ($tool in $script:toolCategories[$cat].Keys) {
        $script:checkState["$cat|$tool"] = $false
    }
}
$script:currentCategory = $script:toolCategories.Keys | Select-Object -First 1

# ==============================================================================
# 3. FUNZIONE DI DOWNLOAD CON PROGRESS REALE
# ==============================================================================
function Invoke-ToolDownload {
    param ([string]$url, [ScriptBlock]$onProgress)

    $fileName = Split-Path $url -Leaf
    $dest = Join-Path $script:folder $fileName

    $wc = New-Object System.Net.WebClient
    $wc.Headers.Add("User-Agent", "Mozilla/5.0")

    $script:dlDone = $false
    $script:dlError = $null

    Register-ObjectEvent -InputObject $wc -EventName DownloadProgressChanged -Action {
        $Event.MessageData.Invoke($EventArgs.ProgressPercentage)
    } -MessageData $onProgress | Out-Null

    Register-ObjectEvent -InputObject $wc -EventName DownloadFileCompleted -Action {
        $script:dlDone = $true
        if ($EventArgs.Error) { $script:dlError = $EventArgs.Error.Message }
    } | Out-Null

    try {
        $wc.DownloadFileAsync([Uri]$url, $dest)
        while (-not $script:dlDone) {
            [System.Windows.Forms.Application]::DoEvents()
            Start-Sleep -Milliseconds 30
        }
    } finally {
        Get-EventSubscriber | Where-Object { $_.SourceObject -eq $wc } | Unregister-Event
        $wc.Dispose()
    }

    if ($script:dlError) { return @{ Success = $false; Error = $script:dlError } }

    if ($fileName -match '\.zip$') {
        try {
            $outDir = Join-Path $script:folder ([IO.Path]::GetFileNameWithoutExtension($fileName))
            if (-not (Test-Path $outDir)) { [void](New-Item -Path $outDir -ItemType Directory -Force) }
            [System.IO.Compression.ZipFile]::ExtractToDirectory($dest, $outDir)
            Remove-Item $dest -Force
        } catch {
            return @{ Success = $false; Error = "Estrazione fallita: $($_.Exception.Message)" }
        }
    }
    return @{ Success = $true }
}

# ==============================================================================
# 4. TEMA / COLORI
# ==============================================================================
$colorBg        = [System.Drawing.Color]::FromArgb(10, 10, 14)
$colorPanel     = [System.Drawing.Color]::FromArgb(18, 18, 24)
$colorPanel2    = [System.Drawing.Color]::FromArgb(24, 24, 32)
$colorCard      = [System.Drawing.Color]::FromArgb(28, 28, 36)
$colorCardSel   = [System.Drawing.Color]::FromArgb(48, 26, 74)
$colorAccent    = [System.Drawing.Color]::FromArgb(168, 62, 255)
$colorAccentDk  = [System.Drawing.Color]::FromArgb(90, 30, 140)
$colorCyan      = [System.Drawing.Color]::FromArgb(0, 191, 255)
$colorText      = [System.Drawing.Color]::FromArgb(235, 235, 240)
$colorTextDim   = [System.Drawing.Color]::FromArgb(150, 150, 160)
$colorGreen     = [System.Drawing.Color]::FromArgb(80, 220, 130)
$colorRed       = [System.Drawing.Color]::FromArgb(230, 80, 90)

$fontTitle  = New-Object System.Drawing.Font("Consolas", 18, [System.Drawing.FontStyle]::Bold)
$fontSub    = New-Object System.Drawing.Font("Consolas", 9)
$fontNormal = New-Object System.Drawing.Font("Segoe UI", 10)
$fontBold   = New-Object System.Drawing.Font("Segoe UI", 10, [System.Drawing.FontStyle]::Bold)
$fontSmall  = New-Object System.Drawing.Font("Consolas", 9)
$fontCard   = New-Object System.Drawing.Font("Segoe UI", 8.5, [System.Drawing.FontStyle]::Bold)

# Palette per le icone generate dei singoli tool
$script:iconPalette = @(
    [System.Drawing.Color]::FromArgb(168, 62, 255),
    [System.Drawing.Color]::FromArgb(0, 191, 255),
    [System.Drawing.Color]::FromArgb(80, 220, 130),
    [System.Drawing.Color]::FromArgb(230, 80, 90),
    [System.Drawing.Color]::FromArgb(255, 170, 40),
    [System.Drawing.Color]::FromArgb(255, 90, 170),
    [System.Drawing.Color]::FromArgb(90, 140, 255),
    [System.Drawing.Color]::FromArgb(120, 220, 220)
)

function Get-ToolInitials {
    param([string]$toolName)
    $caps = [regex]::Matches($toolName, '[A-Z0-9]') | ForEach-Object { $_.Value }
    if ($caps.Count -ge 2) { return ($caps[0] + $caps[1]) }
    elseif ($toolName.Length -ge 2) { return $toolName.Substring(0, 2).ToUpper() }
    else { return $toolName.ToUpper() }
}

function New-ToolIcon {
    param([string]$toolName, [int]$size = 52)
    $colorIdx = [Math]::Abs($toolName.GetHashCode()) % $script:iconPalette.Count
    $color = $script:iconPalette[$colorIdx]
    $initials = Get-ToolInitials -toolName $toolName

    $bmp = New-Object System.Drawing.Bitmap($size, $size)
    $g = [System.Drawing.Graphics]::FromImage($bmp)
    $g.SmoothingMode = [System.Drawing.Drawing2D.SmoothingMode]::AntiAlias
    $g.Clear([System.Drawing.Color]::Transparent)

    $rect = New-Object System.Drawing.Rectangle(1, 1, $size - 2, $size - 2)
    $brush = New-Object System.Drawing.Drawing2D.LinearGradientBrush($rect, $color, [System.Drawing.Color]::FromArgb(255, [Math]::Max($color.R - 40,0), [Math]::Max($color.G - 40,0), [Math]::Max($color.B - 40,0)), 45)
    $g.FillEllipse($brush, $rect)

    $pen = New-Object System.Drawing.Pen([System.Drawing.Color]::FromArgb(60, 255, 255, 255), 1.5)
    $g.DrawEllipse($pen, $rect)

    $fmt = New-Object System.Drawing.StringFormat
    $fmt.Alignment = 'Center'
    $fmt.LineAlignment = 'Center'
    $textFont = New-Object System.Drawing.Font("Segoe UI", ($size / 3.4), [System.Drawing.FontStyle]::Bold)
    $g.DrawString($initials, $textFont, [System.Drawing.Brushes]::White, (New-Object System.Drawing.RectangleF(0,0,$size,$size)), $fmt)

    $g.Dispose()
    $brush.Dispose()
    $pen.Dispose()
    return $bmp
}

# ==============================================================================
# 5. FINESTRA PRINCIPALE (fullscreen, stile app)
# ==============================================================================
$form = New-Object System.Windows.Forms.Form
$form.Text = "Signal X - DFIR & Tools Suite"
$form.MinimumSize = New-Object System.Drawing.Size(1200, 700)
$form.StartPosition = 'CenterScreen'
$form.BackColor = $colorBg
$form.ForeColor = $colorText
$form.FormBorderStyle = 'Sizable'
$form.WindowState = 'Maximized'
$form.Font = $fontNormal

# Logo Signal X incorporato (base64)
$script:logoBase64 = "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"
try {
    $logoBytes = [Convert]::FromBase64String($script:logoBase64)
    $logoStream = New-Object System.IO.MemoryStream(,$logoBytes)
    $script:logoImage = [System.Drawing.Image]::FromStream($logoStream)
    $form.Icon = [System.Drawing.Icon]::FromHandle(([System.Drawing.Bitmap]$script:logoImage).GetHicon())
} catch { $script:logoImage = $null }

# ---- Header ----
$headerPanel = New-Object System.Windows.Forms.Panel
$headerPanel.Dock = 'Top'
$headerPanel.Height = 90
$headerPanel.BackColor = $colorPanel
$form.Controls.Add($headerPanel)

if ($script:logoImage) {
    $picLogo = New-Object System.Windows.Forms.PictureBox
    $picLogo.Image = $script:logoImage
    $picLogo.SizeMode = 'Zoom'
    $picLogo.Size = New-Object System.Drawing.Size(64, 64)
    $picLogo.Location = New-Object System.Drawing.Point(24, 13)
    $picLogo.BackColor = [System.Drawing.Color]::Transparent
    $headerPanel.Controls.Add($picLogo)
    $titleX = 100
} else {
    $titleX = 30
}

$lblTitle = New-Object System.Windows.Forms.Label
$lblTitle.Text = "SIGNAL X — DFIR & SCREENSHARING TOOLS"
$lblTitle.Font = $fontTitle
$lblTitle.ForeColor = $colorAccent
$lblTitle.AutoSize = $true
$lblTitle.Location = New-Object System.Drawing.Point($titleX, 16)
$headerPanel.Controls.Add($lblTitle)

$script:totalToolsCount = ($script:toolCategories.Values | ForEach-Object { $_.Count } | Measure-Object -Sum).Sum

$lblSub = New-Object System.Windows.Forms.Label
$lblSub.Text = "Made by Pieros  |  discord.gg/XVsgKxC4MX  |  $script:totalToolsCount tool disponibili  |  destinazione: $script:folder"
$lblSub.Font = $fontSub
$lblSub.ForeColor = $colorCyan
$lblSub.AutoSize = $true
$lblSub.Location = New-Object System.Drawing.Point($titleX, 54)
$headerPanel.Controls.Add($lblSub)

# Contatore "strumenti selezionati" in alto a destra
$lblCount = New-Object System.Windows.Forms.Label
$lblCount.Name = "lblCount"
$lblCount.Font = New-Object System.Drawing.Font("Segoe UI", 12, [System.Drawing.FontStyle]::Bold)
$lblCount.ForeColor = $colorAccent
$lblCount.Text = "0 strumenti selezionati"
$lblCount.AutoSize = $true
$lblCount.Anchor = 'Top, Right'
$headerPanel.Controls.Add($lblCount)
$script:lblCount = $lblCount

function Position-CountLabel {
    $script:lblCount.Location = New-Object System.Drawing.Point(($headerPanel.Width - $script:lblCount.Width - 30), 20)
}
$headerPanel.Add_Resize({ Position-CountLabel })

$accentBar = New-Object System.Windows.Forms.Panel
$accentBar.Dock = 'Bottom'
$accentBar.Height = 3
$accentBar.BackColor = $colorAccent
$headerPanel.Controls.Add($accentBar)

# ---- Sidebar categorie ----
$sidebar = New-Object System.Windows.Forms.Panel
$sidebar.Dock = 'Left'
$sidebar.Width = 240
$sidebar.BackColor = $colorPanel
$form.Controls.Add($sidebar)

$lblCatHeader = New-Object System.Windows.Forms.Label
$lblCatHeader.Text = "CATEGORIE"
$lblCatHeader.Font = $fontSmall
$lblCatHeader.ForeColor = $colorTextDim
$lblCatHeader.Dock = 'Top'
$lblCatHeader.Height = 30
$lblCatHeader.TextAlign = 'MiddleLeft'
$lblCatHeader.Padding = New-Object System.Windows.Forms.Padding(20, 10, 0, 0)
$sidebar.Controls.Add($lblCatHeader)

$script:categoryButtons = @{}
$catButtonY = 40
foreach ($category in $script:toolCategories.Keys) {
    $toolCount = $script:toolCategories[$category].Count
    $btn = New-Object System.Windows.Forms.Button
    $btn.Text = "  $category"
    $btn.Tag = $category
    $btn.Font = $fontBold
    $btn.FlatStyle = 'Flat'
    $btn.FlatAppearance.BorderSize = 0
    $btn.TextAlign = 'MiddleLeft'
    $btn.Size = New-Object System.Drawing.Size(240, 46)
    $btn.Location = New-Object System.Drawing.Point(0, $catButtonY)
    $btn.BackColor = $colorPanel
    $btn.ForeColor = $colorText
    $btn.Cursor = 'Hand'
    $btn.Add_Click({ Switch-Category -category $this.Tag })
    $sidebar.Controls.Add($btn)

    $lblCatCount = New-Object System.Windows.Forms.Label
    $lblCatCount.Text = "$toolCount"
    $lblCatCount.Font = $fontSmall
    $lblCatCount.ForeColor = $colorTextDim
    $lblCatCount.AutoSize = $false
    $lblCatCount.Size = New-Object System.Drawing.Size(36, 46)
    $lblCatCount.Location = New-Object System.Drawing.Point(200, $catButtonY)
    $lblCatCount.TextAlign = 'MiddleRight'
    $lblCatCount.BackColor = [System.Drawing.Color]::Transparent
    $lblCatCount.Enabled = $false
    $sidebar.Controls.Add($lblCatCount)
    $lblCatCount.BringToFront()

    $script:categoryButtons[$category] = $btn
    $catButtonY += 46
}

if ($script:logoImage) {
    $picSidebarLogo = New-Object System.Windows.Forms.PictureBox
    $picSidebarLogo.Image = $script:logoImage
    $picSidebarLogo.SizeMode = 'Zoom'
    $picSidebarLogo.Size = New-Object System.Drawing.Size(110, 110)
    $picSidebarLogo.Anchor = 'Bottom, Left'
    $picSidebarLogo.Location = New-Object System.Drawing.Point(65, ($form.MinimumSize.Height - 260))
    $picSidebarLogo.BackColor = [System.Drawing.Color]::Transparent
    $sidebar.Controls.Add($picSidebarLogo)
}

# ---- Area centrale ----
$mainArea = New-Object System.Windows.Forms.Panel
$mainArea.Dock = 'Fill'
$mainArea.BackColor = $colorBg
$mainArea.Padding = New-Object System.Windows.Forms.Padding(20)
$form.Controls.Add($mainArea)
$mainArea.BringToFront()

# Barra ricerca + azioni rapide
$toolbarPanel = New-Object System.Windows.Forms.Panel
$toolbarPanel.Dock = 'Top'
$toolbarPanel.Height = 46
$mainArea.Controls.Add($toolbarPanel)

$txtSearch = New-Object System.Windows.Forms.TextBox
$txtSearch.Font = $fontNormal
$txtSearch.BackColor = $colorPanel2
$txtSearch.ForeColor = $colorText
$txtSearch.BorderStyle = 'FixedSingle'
$txtSearch.Size = New-Object System.Drawing.Size(320, 28)
$txtSearch.Location = New-Object System.Drawing.Point(0, 8)
$txtSearch.Text = "Cerca strumento..."
$txtSearch.ForeColor = $colorTextDim
$txtSearch.Add_Enter({ if ($txtSearch.Text -eq "Cerca strumento...") { $txtSearch.Text = ""; $txtSearch.ForeColor = $colorText } })
$txtSearch.Add_Leave({ if ($txtSearch.Text -eq "") { $txtSearch.Text = "Cerca strumento..."; $txtSearch.ForeColor = $colorTextDim } })
$txtSearch.Add_TextChanged({ Refresh-ToolList })
$toolbarPanel.Controls.Add($txtSearch)

$btnSelectAll = New-Object System.Windows.Forms.Button
$btnSelectAll.Text = "Seleziona tutti"
$btnSelectAll.Font = $fontSmall
$btnSelectAll.FlatStyle = 'Flat'
$btnSelectAll.FlatAppearance.BorderColor = $colorAccent
$btnSelectAll.BackColor = $colorPanel2
$btnSelectAll.ForeColor = $colorText
$btnSelectAll.Size = New-Object System.Drawing.Size(140, 28)
$btnSelectAll.Location = New-Object System.Drawing.Point(335, 8)
$btnSelectAll.Cursor = 'Hand'
$btnSelectAll.Add_Click({
    foreach ($tool in $script:toolCategories[$script:currentCategory].Keys) {
        $script:checkState["$script:currentCategory|$tool"] = $true
    }
    Refresh-ToolList
})
$toolbarPanel.Controls.Add($btnSelectAll)

$btnDeselectAll = New-Object System.Windows.Forms.Button
$btnDeselectAll.Text = "Deseleziona tutti"
$btnDeselectAll.Font = $fontSmall
$btnDeselectAll.FlatStyle = 'Flat'
$btnDeselectAll.FlatAppearance.BorderColor = $colorTextDim
$btnDeselectAll.BackColor = $colorPanel2
$btnDeselectAll.ForeColor = $colorText
$btnDeselectAll.Size = New-Object System.Drawing.Size(140, 28)
$btnDeselectAll.Location = New-Object System.Drawing.Point(485, 8)
$btnDeselectAll.Cursor = 'Hand'
$btnDeselectAll.Add_Click({
    foreach ($tool in $script:toolCategories[$script:currentCategory].Keys) {
        $script:checkState["$script:currentCategory|$tool"] = $false
    }
    Refresh-ToolList
})
$toolbarPanel.Controls.Add($btnDeselectAll)

# Griglia di card, una per tool
$cardFlow = New-Object System.Windows.Forms.FlowLayoutPanel
$cardFlow.Dock = 'Fill'
$cardFlow.BackColor = $colorBg
$cardFlow.AutoScroll = $true
$cardFlow.WrapContents = $true
$cardFlow.FlowDirection = 'LeftToRight'
$mainArea.Controls.Add($cardFlow)
$cardFlow.BringToFront()
$script:cardFlow = $cardFlow

function Update-CardVisual {
    param($tilePanel, [bool]$isChecked)
    if ($isChecked) {
        $tilePanel.BackColor = $colorCardSel
        foreach ($ctrl in $tilePanel.Controls) {
            if ($ctrl.Name -eq "badge") { $ctrl.Visible = $true }
        }
    } else {
        $tilePanel.BackColor = $colorCard
        foreach ($ctrl in $tilePanel.Controls) {
            if ($ctrl.Name -eq "badge") { $ctrl.Visible = $false }
        }
    }
}

function Toggle-ToolCard {
    param($tilePanel)
    $tool = $tilePanel.Tag
    $key = "$script:currentCategory|$tool"
    $script:checkState[$key] = -not $script:checkState[$key]
    Update-CardVisual -tilePanel $tilePanel -isChecked $script:checkState[$key]
    Update-SelectedCount
}

$script:cardClickHandler = {
    param($s, $e)
    $tile = if ($s -is [System.Windows.Forms.Panel] -and $s.Tag) { $s } else { $s.Parent }
    Toggle-ToolCard -tilePanel $tile
}

function New-ToolCard {
    param([string]$toolName)

    $card = New-Object System.Windows.Forms.Panel
    $card.Size = New-Object System.Drawing.Size(150, 128)
    $card.Margin = New-Object System.Windows.Forms.Padding(8)
    $card.BackColor = $colorCard
    $card.Tag = $toolName
    $card.Cursor = 'Hand'

    $pic = New-Object System.Windows.Forms.PictureBox
    $pic.Image = New-ToolIcon -toolName $toolName -size 52
    $pic.SizeMode = 'AutoSize'
    $pic.Location = New-Object System.Drawing.Point(49, 14)
    $pic.BackColor = [System.Drawing.Color]::Transparent
    $pic.Cursor = 'Hand'
    $card.Controls.Add($pic)

    $lbl = New-Object System.Windows.Forms.Label
    $lbl.Text = $toolName
    $lbl.Font = $fontCard
    $lbl.ForeColor = $colorText
    $lbl.TextAlign = 'MiddleCenter'
    $lbl.Size = New-Object System.Drawing.Size(140, 40)
    $lbl.Location = New-Object System.Drawing.Point(5, 74)
    $lbl.BackColor = [System.Drawing.Color]::Transparent
    $lbl.Cursor = 'Hand'
    $card.Controls.Add($lbl)

    $badge = New-Object System.Windows.Forms.Label
    $badge.Name = "badge"
    $badge.Text = [char]0x2713
    $badge.Font = New-Object System.Drawing.Font("Segoe UI", 10, [System.Drawing.FontStyle]::Bold)
    $badge.ForeColor = [System.Drawing.Color]::White
    $badge.BackColor = $colorGreen
    $badge.TextAlign = 'MiddleCenter'
    $badge.Size = New-Object System.Drawing.Size(22, 22)
    $badge.Location = New-Object System.Drawing.Point(122, 4)
    $badge.Visible = $false
    $card.Controls.Add($badge)
    $badge.BringToFront()

    $card.Add_Click($script:cardClickHandler)
    $pic.Add_Click($script:cardClickHandler)
    $lbl.Add_Click($script:cardClickHandler)

    return $card
}

function Refresh-ToolList {
    $cardFlow.SuspendLayout()
    $cardFlow.Controls.Clear()
    $filter = $txtSearch.Text
    if ($filter -eq "Cerca strumento...") { $filter = "" }
    foreach ($tool in $script:toolCategories[$script:currentCategory].Keys) {
        if ($filter -eq "" -or $tool -like "*$filter*") {
            $card = New-ToolCard -toolName $tool
            $isChecked = $script:checkState["$script:currentCategory|$tool"]
            Update-CardVisual -tilePanel $card -isChecked $isChecked
            $cardFlow.Controls.Add($card)
        }
    }
    $cardFlow.ResumeLayout()
    Update-SelectedCount
}

function Update-SelectedCount {
    $total = ($script:checkState.Values | Where-Object { $_ }).Count
    $script:lblCount.Text = "$total strumenti selezionati"
    Position-CountLabel
}

function Switch-Category {
    param($category)
    $script:currentCategory = $category
    foreach ($c in $script:categoryButtons.Keys) {
        if ($c -eq $category) {
            $script:categoryButtons[$c].BackColor = $colorAccentDk
            $script:categoryButtons[$c].ForeColor = [System.Drawing.Color]::White
        } else {
            $script:categoryButtons[$c].BackColor = $colorPanel
            $script:categoryButtons[$c].ForeColor = $colorText
        }
    }
    Refresh-ToolList
}

# ---- Pannello inferiore: log + progress + azioni ----
$bottomPanel = New-Object System.Windows.Forms.Panel
$bottomPanel.Dock = 'Bottom'
$bottomPanel.Height = 230
$bottomPanel.BackColor = $colorPanel
$form.Controls.Add($bottomPanel)

$logBox = New-Object System.Windows.Forms.TextBox
$logBox.Multiline = $true
$logBox.ReadOnly = $true
$logBox.ScrollBars = 'Vertical'
$logBox.BackColor = [System.Drawing.Color]::FromArgb(8, 8, 10)
$logBox.ForeColor = $colorTextDim
$logBox.Font = $fontSmall
$logBox.BorderStyle = 'None'
$logBox.Location = New-Object System.Drawing.Point(20, 15)
$logBox.Size = New-Object System.Drawing.Size(600, 155)
$logBox.Anchor = 'Top, Bottom, Left, Right'
$bottomPanel.Controls.Add($logBox)

function Write-Log {
    param([string]$text, [string]$type = "info")
    $prefix = switch ($type) {
        "ok"    { "[OK]  " }
        "err"   { "[ERR] " }
        default { "[..]  " }
    }
    $logBox.AppendText("$prefix$text`r`n")
    $logBox.SelectionStart = $logBox.Text.Length
    $logBox.ScrollToCaret()
}

$script:progressBar = New-Object System.Windows.Forms.ProgressBar
$script:progressBar.Style = 'Continuous'
$script:progressBar.Minimum = 0
$script:progressBar.Maximum = 100
$script:progressBar.Location = New-Object System.Drawing.Point(20, 180)
$script:progressBar.Size = New-Object System.Drawing.Size(600, 18)
$script:progressBar.Anchor = 'Bottom, Left, Right'
$bottomPanel.Controls.Add($script:progressBar)

$script:lblStatus = New-Object System.Windows.Forms.Label
$script:lblStatus.Text = "Pronto."
$script:lblStatus.Font = $fontSmall
$script:lblStatus.ForeColor = $colorCyan
$script:lblStatus.AutoSize = $false
$script:lblStatus.Location = New-Object System.Drawing.Point(20, 200)
$script:lblStatus.Size = New-Object System.Drawing.Size(600, 20)
$script:lblStatus.Anchor = 'Bottom, Left, Right'
$bottomPanel.Controls.Add($script:lblStatus)

$btnDownload = New-Object System.Windows.Forms.Button
$btnDownload.Text = "SCARICA STRUMENTI SELEZIONATI"
$btnDownload.Font = New-Object System.Drawing.Font("Segoe UI", 12, [System.Drawing.FontStyle]::Bold)
$btnDownload.BackColor = $colorAccent
$btnDownload.ForeColor = [System.Drawing.Color]::White
$btnDownload.FlatStyle = 'Flat'
$btnDownload.FlatAppearance.BorderSize = 0
$btnDownload.Size = New-Object System.Drawing.Size(300, 50)
$btnDownload.Location = New-Object System.Drawing.Point(650, 15)
$btnDownload.Anchor = 'Top, Right'
$btnDownload.Cursor = 'Hand'
$bottomPanel.Controls.Add($btnDownload)

$btnOpenFolder = New-Object System.Windows.Forms.Button
$btnOpenFolder.Text = "Apri cartella"
$btnOpenFolder.Font = $fontSmall
$btnOpenFolder.BackColor = $colorPanel2
$btnOpenFolder.ForeColor = $colorText
$btnOpenFolder.FlatStyle = 'Flat'
$btnOpenFolder.FlatAppearance.BorderColor = $colorTextDim
$btnOpenFolder.Size = New-Object System.Drawing.Size(140, 32)
$btnOpenFolder.Location = New-Object System.Drawing.Point(650, 75)
$btnOpenFolder.Anchor = 'Top, Right'
$btnOpenFolder.Cursor = 'Hand'
$btnOpenFolder.Add_Click({ Start-Process explorer.exe $script:folder })
$bottomPanel.Controls.Add($btnOpenFolder)

$btnExit = New-Object System.Windows.Forms.Button
$btnExit.Text = "Esci"
$btnExit.Font = $fontSmall
$btnExit.BackColor = $colorPanel2
$btnExit.ForeColor = $colorRed
$btnExit.FlatStyle = 'Flat'
$btnExit.FlatAppearance.BorderColor = $colorRed
$btnExit.Size = New-Object System.Drawing.Size(140, 32)
$btnExit.Location = New-Object System.Drawing.Point(810, 75)
$btnExit.Anchor = 'Top, Right'
$btnExit.Cursor = 'Hand'
$btnExit.Add_Click({ $form.Close() })
$bottomPanel.Controls.Add($btnExit)

# ==============================================================================
# 6. LOGICA DOWNLOAD
# ==============================================================================
$btnDownload.Add_Click({
    $selectedTools = @()
    foreach ($key in $script:checkState.Keys) {
        if ($script:checkState[$key]) {
            $parts = $key -split '\|', 2
            $cat = $parts[0]; $tool = $parts[1]
            $selectedTools += @{ Name = $tool; Url = $script:toolCategories[$cat][$tool] }
        }
    }

    if ($selectedTools.Count -eq 0) {
        [System.Windows.Forms.MessageBox]::Show("Seleziona almeno un tool prima di procedere!", "Attenzione", [System.Windows.Forms.MessageBoxButtons]::OK, [System.Windows.Forms.MessageBoxIcon]::Warning)
        return
    }

    $btnDownload.Enabled = $false
    $txtSearch.Enabled = $false
    $btnSelectAll.Enabled = $false
    $btnDeselectAll.Enabled = $false
    $logBox.Clear()

    $total = $selectedTools.Count
    $current = 0
    $failures = @()

    foreach ($t in $selectedTools) {
        $current++
        $script:lblStatus.ForeColor = $colorCyan
        $script:lblStatus.Text = "[$current/$total] Scaricando $($t.Name)..."
        $script:progressBar.Value = 0
        [System.Windows.Forms.Application]::DoEvents()

        $progressCb = {
            param($pct)
            $script:progressBar.Value = [Math]::Min([Math]::Max($pct, 0), 100)
        }

        $result = Invoke-ToolDownload -url $t.Url -onProgress $progressCb

        if ($result.Success) {
            Write-Log "$($t.Name) completato" "ok"
        } else {
            Write-Log "$($t.Name) fallito - $($result.Error)" "err"
            $failures += $t.Name
        }
    }

    $script:progressBar.Value = 100
    if ($failures.Count -eq 0) {
        $script:lblStatus.Text = "Download completato ($total/$total)."
        $script:lblStatus.ForeColor = $colorGreen
    } else {
        $script:lblStatus.Text = "Completato con $($failures.Count) errori su $total."
        $script:lblStatus.ForeColor = $colorRed
    }

    $btnDownload.Enabled = $true
    $txtSearch.Enabled = $true
    $btnSelectAll.Enabled = $true
    $btnDeselectAll.Enabled = $true

    $msg = "Download terminato in:`n$script:folder"
    if ($failures.Count -gt 0) { $msg += "`n`nFalliti: $($failures -join ', ')" }
    [System.Windows.Forms.MessageBox]::Show($msg, "Signal X", [System.Windows.Forms.MessageBoxButtons]::OK, [System.Windows.Forms.MessageBoxIcon]::Information)
    Start-Process explorer.exe $script:folder
})

# ==============================================================================
# 7. AVVIO
# ==============================================================================
Switch-Category -category $script:currentCategory
Position-CountLabel
$form.Add_Shown({ $form.Activate() })
$form.ShowDialog() | Out-Null
