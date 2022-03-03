# 资源更新

**更新补丁清单**

对于联机运行模式，在初始化资源系统之后，需要立刻更新资源清单。

在此之前，需要获取更新的资源版本号，一般通过HTTP访问游戏服务器来获取。

````c#
private IEnumerator UpdatePatchManifest()
{
    int updateResourceVersion = 123;
    int timeout = 30;
	UpdateManifestOperation operation = YooAssets.UpdateManifestAsync(updateResourceVersion, timeout);
    yield return operation;
    
    if(operation.Status == EOperationStatus.Succeed)
	{
		//更新成功
	}
	else
	{
        //更新失败
		Debug.LogError(operation.Error);
	}
}
````

**补丁包下载**

在补丁清单更新完毕后，就可以更新资源文件了。

根据产品需求，可以选择更新全部资源，或者只更新部分资源。

````c#
private PatchDownloader _downloader;

/// <summary>
/// 创建下载器
/// </summary>
private void CreateDownloader()
{
	string[] tags = { "buildin", "config" };
    int downloadingMaxNum = 10;
    int failedTryAgain = 3;
	_downloader = YooAssets.CreateDLCDownloader(tags, 10, 3);    
 	if (_downloader.TotalDownloadCount == 0)
    {
        //没有需要下载的资源
    }
    else
    {
        //需要下载的文件总数和总大小
       	int totalDownloadCount = _downloader.TotalDownloadCount;
		long totalDownloadBytes = _downloader.TotalDownloadBytes;
    }
}

/// <summary>
/// 更新下载器
/// </summary>
private void UpdateDownloader()
{
    if(_downloader != null)
        _downloader.Update();
}

/// <summary>
/// 开启下载
/// </summary>
private IEnumerator Download()
{
	//注册下载回调
	_downloader.OnPatchFileDownloadFailedCallback = OnPatchFileDownloadFailed;
	_downloader.OnDownloadProgressCallback = OnDownloadProgressUpdate;
    _downloader.OnDownloadOverCallback = OnDownloadOver;
	_downloader.Download();
	yield return _downloader;

	//检测下载结果
	if (_downloader.DownloadStates == EDownloaderStates.Succeed)
    {
        //下载成功
    }
    else      
    {
        //下载失败
    }
}

/// <summary>
/// 文件下载失败
/// </summary>
private void OnPatchFileDownloadFailed(string fileName)
{
    Debug.LogError($"File download failed : {fileName}");
}

/// <summary>
/// 下载进度的更新
/// </summary>
private void OnDownloadProgressUpdate(int totalDownloadCount, int currentDownloadCount, long totalDownloadSizeBytes, long currentDownloadSizeBytes)
{
}

/// <summary>
/// 下载结束，成功或失败
/// </summary>
private void OnDownloadOver(bool isSucceed)
{
}
````
