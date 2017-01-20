# Overview

## [Introduction](storage-introduction.md)

# Get Started

## [Quick start guide](storage-getting-started-guide.md)
## [Create a storage account](storage-create-storage-account.md)

## Blob Storage
### [.NET](storage-dotnet-how-to-use-blobs.md)
### [Java](storage-java-how-to-use-blob-storage.md)
### [Node.js](storage-nodejs-how-to-use-blob-storage.md)
### [C++](storage-c-plus-plus-how-to-use-blobs.md)
### [Python](storage-python-how-to-use-blob-storage.md)
### [PHP](storage-php-how-to-use-blobs.md)
### [Ruby](storage-ruby-how-to-use-blob-storage.md)
### [iOS](storage-ios-how-to-use-blob-storage.md)
### [Xamarin](storage-xamarin-blob-storage.md)

## Queue Storage
### [.NET](storage-dotnet-how-to-use-queues.md)
### [Java](storage-java-how-to-use-queue-storage.md)
### [Node.js](storage-nodejs-how-to-use-queues.md)
### [C++](storage-c-plus-plus-how-to-use-queues.md)
### [Python](storage-python-how-to-use-queue-storage.md)
### [PHP](storage-php-how-to-use-queues.md)
### [Ruby](storage-ruby-how-to-use-queue-storage.md)

## Table Storage
### [.NET](storage-dotnet-how-to-use-tables.md)
### [Java](storage-java-how-to-use-table-storage.md)
### [Node.js](storage-nodejs-how-to-use-table-storage.md)
### [C++](storage-c-plus-plus-how-to-use-tables.md)
### [Python](storage-python-how-to-use-table-storage.md)
### [PHP](storage-php-how-to-use-table-storage.md)
### [Ruby](storage-ruby-how-to-use-table-storage.md)

## File Storage
### [Windows, .NET, PowerShell](storage-dotnet-how-to-use-files.md)
### [Linux](storage-how-to-use-files-linux.md)
### [Java](storage-java-how-to-use-file-storage.md)
### [C++](storage-c-plus-plus-how-to-use-files.md)
### [Python](storage-python-how-to-use-file-storage.md)

# How To
## [Create a storage account](storage-create-storage-account.md)
## Use blobs
### [Service overview](https://msdn.microsoft.com/library/dd179376.aspx)
### [Hot and cool tiers](storage-blob-storage-tiers.md)
### [Custom domains](storage-custom-domain-name.md)
### [Anonymous access to blobs](storage-manage-access-to-resources.md)
### [Samples](https://azure.microsoft.com/documentation/samples/?service=storage&term=blob)
## Use queues
### [Concepts](https://msdn.microsoft.com/library/dd179353.aspx)
### [Samples](https://azure.microsoft.com/documentation/samples/?service=storage&term=queue)
## Use tables
### [Overview](https://msdn.microsoft.com/library/dd179463.aspx)
### [Table design guide](storage-table-design-guide.md)
### [Samples](https://azure.microsoft.com/documentation/samples/?service=storage&term=table)
## Use files
### [Overview](https://msdn.microsoft.com/en-us/library/dn166972.aspx)
### [Troubleshoot Azure Files](storage-troubleshoot-file-connection-problems.md)
### [Samples](https://azure.microsoft.com/documentation/samples/?service=storage&term=file)
## Use Virtual Machine Disks
### Premium Storage
#### [High-performance storage for VM workloads](storage-premium-storage.md)
#### [Migrate to Premium Storage](storage-migration-to-premium-storage.md)
#### [Design for high performance](storage-premium-storage-performance.md)
### Standard Storage
#### [Back up VM disks with incremental snapshots](storage-incremental-snapshots.md)
## Plan and design
### [Replication](storage-redundancy.md)
### [Scalability and performance targets](storage-scalability-targets.md)
### [Performance and scalability checklist](storage-performance-checklist.md)
### [Concurrency](storage-concurrency.md)
## Develop
### [Samples](storage-samples.md)
### [Configure connection strings](storage-configure-connection-string.md)
### [Use the Storage Emulator](storage-use-emulator.md)
### [Set and retrieve properties and metadata](storage-properties-metadata.md)
## Manage
### [PowerShell](storage-powershell-guide-full.md)
### [Azure CLI 2.0 (Preview)](storage-azure-cli.md)
### [Azure CLI 1.0](storage-azure-cli-nodejs.md)
### [Azure Automation](automation-manage-storage.md)
## Secure
### [Security guide](storage-security-guide.md)
### [Encryption for data at rest](storage-service-encryption.md)
### [Shared key authentication](https://msdn.microsoft.com/library/dd179428.aspx)
### [Shared access signatures (SAS)](storage-dotnet-shared-access-signature-part-1.md)
### [Tutorial: Encrypt and decrypt blobs using Azure Key Vault](storage-encrypt-decrypt-blobs-key-vault.md)
### Client-side encryption
#### [.NET](storage-client-side-encryption.md)
#### [Java](storage-client-side-encryption-java.md)
#### [Python](storage-client-side-encryption-python.md)
## Monitor and troubleshoot
### Metrics and logging
#### [Storage Analytics](storage-analytics.md)
#### [Enable and view metrics](storage-enable-and-view-metrics.md)
#### [Monitor, diagnose, and troubleshoot](storage-monitoring-diagnosing-troubleshooting.md)
#### [Troubleshooting tutorial](storage-e2e-troubleshooting.md)
### Troubleshoot disk deletion errors
#### [In a Resource Manager deployment](storage-resource-manager-cannot-delete-storage-account-container-vhd.md)
#### [In a classic deployment](storage-cannot-delete-storage-account-container-vhd.md)
### [Troubleshoot File storage](storage-troubleshoot-file-connection-problems.md)
### [Disaster recovery guidance](storage-disaster-recovery-guidance.md)
## Transfer Data
### [Move data to and from Storage](storage-moving-data.md)
### [AzCopy command-line utility](storage-use-azcopy.md)
### [Using the Import-Export service](storage-import-export-service.md)
### [Using the Import-Export Tool](storage-import-export-tool-how-to.md)
#### [Setting up the Import-Export Tool](storage-import-export-tool-setup.md)
#### [Preparing hard drives for an import job](storage-import-export-tool-preparing-hard-drives-import.md)
##### [Setting Properties and Metadata during the import process](storage-import-export-tool-setting-properties-metadata-import.md)
##### [Sample workflow to prepare hard drives for an import job](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow.md)
##### [Quick reference for frequently used commands for import jobs](storage-import-export-tool-quick-reference.md)
#### [Previewing drive usage for an export job](storage-import-export-tool-previewing-drive-usage-export-v1.md)
#### [Reviewing job status with copy log files](storage-import-export-tool-reviewing-job-status-v1.md)
#### [Repairing an import job](storage-import-export-tool-repairing-an-import-job-v1.md)
#### [Repairing an export job](storage-import-export-tool-repairing-an-export-job-v1.md)
#### [Troubleshooting the Import-Export Tool](storage-import-export-tool-troubleshooting-v1.md)
#### [Import-Export Service Manifest File format](storage-import-export-file-format-manifest.md)
#### [Import-Export Service Metadata and Properties File format](storage-import-export-file-format-metadata-and-properties.md)
#### [Import-Export Service Log File format](storage-import-export-file-format-log.md)
### [Using the Import-Export Tool (v1)](storage-import-export-tool-how-to-v1.md)
#### [Setting up the Import-Export Tool](storage-import-export-tool-setup-v1.md)
#### [Preparing hard drives for an import job](storage-import-export-tool-preparing-hard-drives-import-v1.md)
##### [Setting Properties and Metadata during the import process](storage-import-export-tool-setting-properties-metadata-import-v1.md)
##### [Sample workflow to prepare hard drives for an import job](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow-v1.md)
##### [Quick reference for frequently used commands for import jobs](storage-import-export-tool-quick-reference-v1.md)
#### [Previewing drive usage for an export job](storage-import-export-tool-previewing-drive-usage-export-v1.md)
#### [Reviewing job status with copy log files](storage-import-export-tool-reviewing-job-status-v1.md)
#### [Repairing an import job](storage-import-export-tool-repairing-an-import-job-v1.md)
#### [Repairing an export job](storage-import-export-tool-repairing-an-export-job-v1.md)
#### [Troubleshooting the Import-Export Tool](storage-import-export-tool-troubleshooting-v1.md)
#### [Import-Export Service Manifest File format](storage-import-export-file-format-manifest.md)
#### [Import-Export Service Metadata and Properties File format](storage-import-export-file-format-metadata-and-properties.md)
#### [Import-Export Service Log File format](storage-import-export-file-format-log.md)
### [Using the Azure Import-Export Service REST API](storage-import-export-using-the-rest-api.md)
#### [Creating an import job](storage-import-export-creating-an-import-job.md)
#### [Creating an export job](storage-import-export-creating-an-export-job.md)
#### [Retrieving state information for a job](storage-import-export-retrieving-state-info-for-a-job.md)
#### [Enumerating jobs](storage-import-export-enumerating-jobs.md)
#### [Cancelling and deleting jobs](storage-import-export-cancelling-and-deleting-jobs.md)
#### [Backing Up Drive Manifests](storage-import-export-backing-up-drive-manifests.md)
#### [Diagnostics and Error Recovery for Import-Export jobs](storage-import-export-diagnostics-and-error-recovery.md)
# Reference
## [PowerShell](/powershell/storage)
## [Azure CLI](/cli/azure/storage)
## .NET
### [Resource manager](/dotnet/api/microsoft.azure.management.storage)
### [Data movement](https://msdn.microsoft.com/en-us/library/azure/mt684990.aspx)
### [Blobs, Queues, Tables, and Files](https://msdn.microsoft.com/library/azure/mt347887.aspx)
## [Java](/java/api/)
## [Node.js](http://azure.github.io/azure-storage-node)
## [Ruby](http://azure.github.io/azure-storage-ruby)
## [Python](https://azure-storage.readthedocs.io/en/latest/index.html)
## [C++](http://azure.github.io/azure-storage-cpp)
## [iOS](https://github.com/Azure/azure-storage-ios)
## [Android](http://azure.github.io/azure-storage-android)
## REST
### [Blobs, Queues, Tables, and Files](/rest/api/storageservices/fileservices/azure-storage-services-rest-api-reference)
### [Resource provider](/rest/api/storagerp)
### [Import/export](/rest/api/storageimportexport)

# Related
## Classic Portal
### [Create storage account](storage-create-storage-account-classic-portal.md)
### [Enable and view metrics](storage-enable-and-view-metrics-classic-portal.md)
### [Monitor, diagnose, and troubleshoot](storage-monitoring-diagnosing-troubleshooting-classic-portal.md)
### [Troubleshooting tutorial](storage-e2e-troubleshooting-classic-portal.md)

# Resources
## [Pricing](https://azure.microsoft.com/pricing/details/storage/blobs/)
## [Azure Storage client tools](storage-explorers.md)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/windows-azure-storage)
## [Forum](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata)
## [Service updates](https://azure.microsoft.com/updates/?product=storage)
## [Videos](https://azure.microsoft.com/documentation/videos/index/?services=storage)

## Azure Storage Explorer
### [Storage Explorer (Preview)](../vs-azure-tools-storage-manage-with-storage-explorer.md)
### [Manage blobs with Storage Explorer (Preview)](../vs-azure-tools-storage-explorer-blobs.md)

## Nuget packages
### [Azure Storage Client Library for .NET](https://www.nuget.org/packages/WindowsAzure.Storage/)
### [Azure Storage Data Movement Library](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement/)
### [Azure Configuration Manager](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/)

## Source code
### .NET
#### [Blob, queue, table, and file](https://github.com/Azure/azure-storage-net)
#### [Data movement](https://github.com/Azure/azure-storage-net-data-movement)
#### [Resource provider](https://github.com/Azure/azure-sdk-for-net)
### [Node.js](http://azure.github.io/azure-storage-node/)
### [Java](https://github.com/Azure/azure-storage-java)
### [C++](https://github.com/Azure/azure-storage-cpp)
### [PHP](https://github.com/Azure/azure-storage-php)
### [Ruby](https://github.com/Azure/azure-storage-ruby)
### [Python](https://github.com/Azure/azure-storage-python)
### [iOS](https://github.com/Azure/azure-storage-ios)
