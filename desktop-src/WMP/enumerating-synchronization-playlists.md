---
title: Enumerating Synchronization Playlists
description: Enumerating Synchronization Playlists
ms.assetid: '830c3ea5-2937-48b5-b89f-ef68a6649ca3'
keywords: ["Windows Media Player,synchronization playlists", "Windows Media Player object model,synchronization playlists", "object model,synchronization playlists", "Windows Media Player Mobile,synchronization playlists", "Windows Media Player ActiveX control,synchronization playlists", "Windows Media Player Mobile ActiveX control,synchronization playlists", "ActiveX control,synchronization playlists", "playlists,synchronization", "metafile playlists,synchronization", "Windows Media metafile playlists,synchronization", "synchronization playlists,enumerating", "portable devices,enumerating", "enumerations,synchronization playlists"]
---

# Enumerating Synchronization Playlists

The following example code creates a function that fills a ListView control with playlists. Synchronization playlists appear first. Synchronization playlists for the currently selected device are marked with a check mark and are sorted in synchronization priority order. All other playlists are unchecked.

The ListView control was configured for single selection. The order of playlists in the ListView control determines their synchronization priority. The checked state of an individual playlist determines whether it is a synchronization playlist for the currently selected device.

The parameter *lPSIndex* specifies the partnership index for the currently selected portable device.


```C++
STDMETHODIMP CSyncSettings::ShowPlaylists(long lPSIndex)
{ 
    HRESULT hr = S_OK; 
    ATLASSERT(m_pMainDlg);
   
    CComPtr<IWMPMediaCollection> spMediaCollection;
    CComPtr<IWMPPlaylist> spPlaylist; // Contains a playlist of all playlists.
    long lSyncItemCount= 0; // Count of items selected for synchronization. 

    ListView_DeleteAllItems(m_hPlView);

    m_spPlaylist.Release();
   
    if(lPSIndex == 0)
    {
        return S_OK;
    } 

    HCURSOR hCursor = LoadCursor(NULL, IDC_WAIT);
    HCURSOR hCursorOld = SetCursor(hCursor);

    if(SUCCEEDED(hr))
    {
        // Retrieve a pointer to IWMPMediaCollection.
        hr = m_spPlayer->get_mediaCollection(&amp;spMediaCollection);
    }

    if(SUCCEEDED(hr) &amp;&amp; spMediaCollection)
    {
        // Retrieve a playlist filled with IWMPMedia items.
        // Each IWMPMedia represents an individual playlist 
        // in the library.
        hr = spMediaCollection->getByAttribute(CComBSTR("MediaType"), CComBSTR("playlist"), &amp;spPlaylist);
    }
 
    if(SUCCEEDED(hr) &amp;&amp; spPlaylist)
    {  
        // Get the sync attribute name for the current device.
        CComBSTR bstrAttribute(g_szSyncAttributeNames[lPSIndex]);

        // Sort the playlist.
        // lSyncItemCount is the count of synchronization playlists.
        hr = SortPlaylist(spPlaylist, bstrAttribute, &amp;lSyncItemCount);
     
        if(SUCCEEDED(hr))
        {
            m_spPlaylist = spPlaylist;  // Cache the playlist.

            long lCount = 0;
            spPlaylist->get_count(&amp;lCount);
             
            // Fill the ListView control.
            for(long i = 0; i < lCount; i++)
            {
                CComPtr<IWMPMedia> spMedia;
                CComBSTR bstrPlaylistName;
                CComBSTR bstrVal; // Value of the SyncXX attribute.

                // Retrieve a playlist.
                hr = spPlaylist->get_item(i, &amp;spMedia);

                if(SUCCEEDED(hr) &amp;&amp; spMedia)
                {
                    // Get the name.
                    hr = spMedia->get_name(&amp;bstrPlaylistName);
                }  
                if(SUCCEEDED(hr) &amp;&amp; spMedia)
                {      
                    // Get the SyncXX attribute value.
                    hr = spMedia->getItemInfo(bstrAttribute, &amp;bstrVal);
                }

                if(SUCCEEDED(hr) &amp;&amp; bstrPlaylistName.Length())
                {
                    // Insert the item in the ListView control.
                    LVITEM lvI;
                    ZeroMemory(&amp;lvI, sizeof(LVITEM));
                    lvI.mask = LVIF_TEXT; 
                    lvI.lParam = _wtol(bstrVal);
                    lvI.iItem = ListView_GetItemCount(m_hPlView);
                    lvI.pszText = "";
                    ListView_InsertItem(m_hPlView, &amp;lvI);

                    // If this is a sync playlist, mark the check box.
                    if(i < lSyncItemCount)
                    {
                        ListView_SetCheckState(m_hPlView, i, TRUE);
                    }

                    // Display the playlist name.
                    lvI.iSubItem = 1;
                    lvI.pszText = COLE2T(bstrPlaylistName);
                    ListView_SetItem(m_hPlView, &amp;lvI);
                }
            }
        }        
    }

    SetCursor(hCursorOld);
    return hr;
}
```



For the implementation of the SortPlaylist function, see [Sorting Playlists by Synchronization Priority](sorting-playlists-by-synchronization-priority.md).

## Related topics

<dl> <dt>

[**IWMPMedia::getItemInfo**](iwmpmedia-getiteminfo.md)
</dt> <dt>

[**IWMPMediaCollection::getByAttribute**](iwmpmediacollection-getbyattribute.md)
</dt> <dt>

[**Managing Synchronization Playlists**](managing-synchronization-playlists.md)
</dt> <dt>

[**Sync Attributes**](sync-attributes.md)
</dt> </dl>

 

 



