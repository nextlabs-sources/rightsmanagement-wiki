# **Remote Viewer** #

    By Gavin Ye
    8/8/2017



## Table of Content

- [Table of Content](#table-of-content)
- [1. Overview](#1-overview)
- [2. Source](#2-source)
- [3. Design](#3-implementation)
    - [3.1 Design Overview](#31-design-overview)
    - [3.2 Launch](#32-launch)
        - [3.2.1 Parameters](#321-parameters)
        - [3.2.2 Cookis](#322-cookies)
    - [3.3 Browser Settings](#33-browser-settings)

## 1. Overview

RMC use remote viewer as the basic viewer to view remote or local files.

## 2. Source

All the source code is under `"/RMC-Windows/source/libraries/nxrviewer"`

## 3. Design

### 3.1 Design Overview

The remote viewer is using MFC `CHtmlDialog`. The main window host an embedded IE browser, and navigate to the URL passed in Process Environment Block.

### 3.2 Launch

The remote viewer should always be lauched by NXRMC which also pass necessary parameters to viewer in Process Environment Block.

See `"viewermgr.h" and "session_viewer.cpp"` in `"librmc"`.

#### 3.2.1 Parameters

Parameters are encrypted, base64 encoded and stored in Process Environment Block.

- X_NXRM_VIEWER_TYPE

    It decides the viewer's type. Valid values are:

    - RemoteViewer (0)
    - LocalViewer (1)

- X_NXRM_VIEWER_PARAMS

    This is an AES encrypted and base64 encoded json data.

#### 3.2.2 Cookies

Remote Viewer need to set all the cookies passed in Process Environment Block.

See code in `BOOL CViewMainDlg::OnInitDialog()`.


### 3.3 Browser Settings

Some features should be enabled or disabled for the embedded IE browser.

- FEATURE_BROWSER_EMULATION

    The value should be set to 11000 which indicates IE 11.

- FEATURE_GPU_RENDERING

    The value should be set to 1 which indicates GPU Rendering is enabled.

- FEATURE_NINPUT_LEGACYMODE

    The value should be set to 0 which indicates legacy input is disabled.
