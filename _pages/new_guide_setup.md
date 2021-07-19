---
permalink: /new_documentation
title: "Planning new documentation"
---

# Setting up a new book or renaming books

Follow these instructions if you have to set up a new book/guide or need to rename guides.

**Prerequisites**: You must have access to the following tools and directories:

* GitHub
* The `open-cluster-management` repository
* Red Hat VPN
* Pantheon
* GitLab

**Important**: Do you really need a new guide? See the documentation architect to discuss placement of new content.

To create a new guide in the product documentation, you must create a new folder in your repository, and a new book in Pantheon. When you create a new folder, you must also create a `master.adoc` and `docinfo.xml` file for your new folder. See the repository for examples. 

## Creating a new folder 

**Create a new folder from GitHub**

1. Log in to GitHub and navigate to the `open-cluster-management/rhacm-doc` repository. 
2. Click the **Add file** button > **Create new file**. 
3. When you enter the name of the folder, select the forward slash (`/`) to continue adding a file to your folder. 
4. Add the `master.adoc` file for your folder. The contents of your file might resemble the following file:

   ```
   include::modules/common-attributes.adoc[]

   = Book/guide name

   include::intro.adoc[leveloffset=+1]
   include::guide_task1.adoc[leveloffset=+2]
   include::guide_task2.adoc[leveloffset=+2]
   ```

5. Add the `docinfo.xml` file to your new folder. Your `docinfo.xm`l file might resemble the following information:

   ```
   <productname>{product-title}</productname>
   <productnumber>{product-version}</productnumber>
   <subtitle>Manage cluster</subtitle>
   <abstract>
       <para>Manage cluster in {product-title}</para>
   </abstract>
   <authorgroup>
       <orgname>{product-title} Team</orgname>
   </authorgroup>
   <xi:include href="Common_Content/Legal_Notice.xml" xmlns:xi="http://www.w3.org/2001/XInclude" />
   ```

**Create a new folder from your text editor**
(VS Code or Atom is recommended to preview, prevent errors, and work in collaboration with dev.)

1. Verify that you are in the `rhacm-docs` repository by running the following command from your terminal:
   
   ```
   pwd
   ```
   
   Your path might resemble the following content:
   
   ```
   /Users/local-cpu-name/rhacm-docs
   ```
   
2. If you are using Visual Studio Code (VSC), select the **New Folder** icon to create a folder. 

### Add a _symlink_ to the _modules_ folder 

You must add a `symlink` to the `modules` folder in your new folder to resolve the conrefs. You can copy a file from another folder in the editor, or see the following steps (Mac only):

1. Open a terminal and navigate to the new GitHub folder.

2. Enter the following command:
   ```
   ln -s ../modules modules
   ```
   
3. Commit the change to your Git repository.

## Set up a new book in Pantheon

You must complete the _Set up new title_ form to create a new book. Log in to [Pantheon](https://pantheon.int.us-west.aws.prod.paas.redhat.com/#/titles/red_hat_advanced_cluster_management_for_kubernetes) and select the **Set up a new title** button. 

Complete the following steps to complete the _New title_ form:
   
   1. Verify if you are setting up a new folder from an existing repository in GitLab by selecting one of the options that is provided. If you are creating a new book in a set of books that is already building correctly, select the option for an already set up environment. 
   2. If the repository exists in GitLab, values for the product title and version number are complete.
   3. From the _GitLab Group_ section click the drop-down button and select **Documentation - Red Hat OpenShift Enterprise**.
   4. For _GitLab Project_, select **Red Hat Advanced Cluster Management for Kubernetes**.
   5. Select **1.0** for the preview branch. This one is not used, but requires a value. 
   6. For the _Stage Branch_, select the release number for your book. For example, select **2.1**.
   7. Enter information for the *Content Title*, which is your book title. Example: Install.
   8. Select **Book** for the _Content Type_.
   9. Ensure that the checkbox for _Use default content directory_ is selected, also select `AsciiDoc` as the _Content Markup_.
   10. You can add multiple new titles by selecting the **Add additional title** button. Otherwise, select **Submit** when the form is complete. 
   11. Run a manual build to pick up the new book. For more information, see [Build and publish](arch_builds.md#build-and-publish).

*Possible troubleshooting steps for the `no main file` error message after building the new book:*
    1. In the Pantheon interface, select the edit (pencil) icon beside your new book. It appears when you hover over the book title. 
    2. Verify that there is also a **Title URL fragment** listed in the field. It is generally an all lowercase version of the **Title name** that is listed.
    3. If it is not there, add it.
    4. If that doesn't resolve the error with your next build, try deleting and recreating the symlink in the folder of the new book. 

## Renaming 

Only rename a folder/guide when absolutely needed as a result of product change.

   1. Rename the folder in the source, as Git directory must match the URL. (Git remains the source of truth.)
   2. Rename the guide in the `docinfo.xml` file.
   3. Rename the title of the `master.adoc`.
   4. Using find/replace, change all links that are impacted.
   5. Log in to Pantheon.
   6. Click **Edit title**.
   7. Change the name and the URL fragment. Keep URL fragment in lowercase.
   8. Click **Rename** to save.
   9. Select the drop-downn error for the book and click **Edit Configuration**.
   10. Update the _Content Directory_ field with the new name of the folder.
   11. When links and files/folders are all changed, as well as titles, run a build.
   

