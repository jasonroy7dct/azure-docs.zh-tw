---
title: 教學課程：Azure Active Directory 單一登入 (SSO) 與 Pantheon 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Pantheon 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d2c965d1-666f-44c2-b08f-b73163096374
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/05/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f24cfeac35e5ce669575c14a3de3e2bff46c7a91
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/06/2019
ms.locfileid: "74893241"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-pantheon"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 Pantheon 整合

在本教學課程中，您會了解如何整合 Pantheon 與 Azure Active Directory (Azure AD)。 在整合 Pantheon 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 Pantheon 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 Pantheon。
* 在 Azure 入口網站集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>必要條件

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用 Pantheon 單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。


* Pantheon 支援由 **IDP** 起始的 SSO




## <a name="adding-pantheon-from-the-gallery"></a>從資源庫新增 Pantheon

若要設定 Pantheon 與 Azure AD 整合，您需要從資源庫將 Pantheon 新增至受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory]  服務。
1. 巡覽至 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增]  區段的搜尋方塊中輸入 **Pantheon**。
1. 從結果面板選取 [Pantheon]  ，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。


## <a name="configure-and-test-azure-ad-single-sign-on-for-pantheon"></a>設定及測試 Pantheon 的 Azure AD 單一登入

以名為 **B.Simon** 的測試使用者，設定及測試與 Pantheon 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 Pantheon 中相關使用者之間的連結關聯性。

若要設定及測試與 Pantheon 搭配運作的 Azure AD SSO，請完成下列建置組塊：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    1. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 B.Simon 測試 Azure AD 單一登入。
    1. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 B.Simon 能夠使用 Azure AD 單一登入。
1. **[設定 Pantheon SSO](#configure-pantheon-sso)** - 在應用程式端設定單一登入設定。
    1. **[建立 Pantheon 測試使用者](#create-pantheon-test-user)** - 使 Pantheon 中對應的 B.Simon 連結到該使用者在 Azure AD 中的代表項目。
1. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Pantheon]  應用程式整合頁面上，尋找 [管理]  區段並選取 [單一登入]  。
1. 在 [**選取單一登入方法**] 頁面上，選取 [**SAML**]。
1. 在 [以 SAML 設定單一登入]  頁面上，按一下 [基本 SAML 設定]  的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [以 SAML 設定單一登入]  頁面上，輸入下列欄位的值：

    a. 在 [識別碼]  文字方塊中，使用下列模式來輸入 URL：`urn:auth0:pantheon:<orgname>-SSO`

    b. 在 [回覆 URL]  文字方塊中，使用下列模式來輸入 URL：`https://pantheon.auth0.com/login/callback?connection=<orgname>-SSO`

    > [!NOTE]
    > 這些都不是真正的值。 請使用實際的識別碼和回覆 URL 更新這些值。 請連絡 [Pantheon 用戶端支援小組](https://pantheon.io/docs/getting-support/)以取得這些值。 您也可以參考 Azure 入口網站中**基本 SAML 組態**區段所示的模式。

1. Pantheon 應用程式需要特定格式的 SAML 判斷提示，而您需要將自訂屬性對應新增到 SAML 權杖屬性組態。 下列螢幕擷取畫面顯示預設屬性清單，其中的 **nameidentifier** 與 **user.userprincipalname** 相對應。 Pantheon 應用程式要求 **nameidentifier** 需與 **user.mail** 相對應，因此您必須按一下 [編輯]  圖示以編輯屬性對應，並變更屬性對應。

    ![image](common/edit-attribute.png)

1. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，尋找 [憑證 (Base64)]  並選取 [下載]  ，以下載憑證並將其儲存在電腦上。

    ![憑證下載連結](common/certificatebase64.png)

1. 在 [設定 Pantheon]  區段上，根據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

在本節中，您將在 Azure 入口網站中建立名為 B.Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]  、[使用者]  和 [所有使用者]  。
1. 在畫面頂端選取 [新增使用者]  。
1. 在 [使用者]  屬性中，執行下列步驟：
   1. 在 [名稱]  欄位中，輸入 `B.Simon`。  
   1. 在 [使用者名稱]  欄位中，輸入 username@companydomain.extension。 例如： `B.Simon@contoso.com` 。
   1. 選取 [顯示密碼]  核取方塊，然後記下 [密碼]  方塊中顯示的值。
   1. 按一下頁面底部的 [新增]  。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Pantheon 的存取權授與 B.Simon，讓其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [Pantheon]  。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。

   ![[使用者和群組] 連結](common/users-groups-blade.png)

1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中選取 [使用者和群組]  。

    ![[新增使用者] 連結](common/add-assign-user.png)

1. 在 [使用者和群組]  對話方塊的 [使用者] 清單中選取 [B.Simon]  ，然後按一下畫面底部的 [選取]  按鈕。
1. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色]  對話方塊的清單中為使用者選取適當的角色，然後按一下畫面底部的 [選取]  按鈕。
1. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

## <a name="configure-pantheon-sso"></a>設定 Pantheon SSO

若要在 **Pantheon** 端設定單一登入，您必須將已下載的「憑證」  和已複製的適當 URL 傳送給 [Pantheon 支援小組](https://pantheon.io/docs/getting-support/)。

> [!Note]
> 當您想要啟用這個連接時，您也需要提供電子郵件網域資訊和日期時間。 您可以從[這裡](https://pantheon.io/docs/sso-organizations/)找到更多詳細資訊

### <a name="create-pantheon-test-user"></a>建立 Pantheon 測試使用者

在本節中，您要在 Pantheon 中建立名為 B.Simon 的使用者。 請遵循下列步驟，在 Pantheon 中新增使用者。 

>[!NOTE] 
>必須先在 Pantheon 中建立使用者，SSO 才有作用。

1. 以管理員認證登入 Pantheon。

2. 瀏覽至 [組織]  儀表板頁面。
 
3. 按一下 [人員]  。

4. 按一下 [新增使用者]  。

5. 輸入使用者的電子郵件地址。

6. 選擇使用者的角色。

7. 按一下 [新增使用者]  。

## <a name="test-sso"></a>測試 SSO 

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 Pantheon 圖格時，應該會自動登入您已設定 SSO 的 Pantheon。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [嘗試搭配 Azure AD 使用 Pantheon](https://aad.portal.azure.com/)

