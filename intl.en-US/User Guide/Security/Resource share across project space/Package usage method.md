# Package usage method {#concept_vj4_rk1_wdb .concept}

## Package usage method {#section_y4q_l11_hfb .section}

The use of package involves two subjects: the package creator and the package user.

-   The package creator provides the resources to be shared and the permissions to access it. It also allows the package user to install and use it.
-   The package user uses the package. After the package is published, the user can directly access the resource across projects.

The following is a description of the operations involved with the package creator and package user.

## Package creator {#section_sbs_1l1_wdb .section}

-   Create package

    ```
    Create package;
    ```

    **Note:** 

    -   Only the project owner has the permission to create a package.
    -   The name of the package cannot exceed 128 characters.
-   Add a resource to be shared to the package

    ```
        Add project_object to package package_name [with privileges] -- add objects to package
        Remove project_object from package package_name; -- remove object from package
        project_object ::= table table_name |
                       instance inst_name |
                       function func_name |
                       resource res_name
        privileges ::= action_item1, action_item2, ...
    ```

    **Note:** 

    -   Currently, supported types of objects exclude projects. Therefore, you cannot use a package to create objects in other projects.
    -   The objects themselves and the permission to perform operations on them are added to the package at the same time. When not passed \(with privileges\) even specifying an action permission, the default is read-only, that is, read/describe/select. The object and its permissions are treated as a whole and cannot be updated once added. If necessary, you can only delete and re-add.
    -   When an object is added to a package, it is not packaged as a snapshot, so subsequent object data changes, and access to the object through package authorization is also the current data of the object.
    Use the following commands to perform various operations on the package:

-   Allow other projects to use a package

    ```
    allow project <prjName> to install package <pkgName> [using label <num>]
    ```

-   Revoke other projects’ permission to use a package

    ```
    disallow project <prjName> to install package <pkgName>
    ```

-   Drop a package

    ```
    Delete package <pkgname>;
    ```

-   View the list of packages already created and installed

    ```
    Show packages;
    ```

-   View package details

    ```
    Describe package <pkgname>;
    ```


## Package users {#section_kwk_fl1_wdb .section}

-   Install package

    ```
    Install package <pkgname>;
    ```

    For package installation, the pkgName format is: <projectName\>.<packageName\>.

    **Note:** Only the project owner has permissions to perform this operation.

-   Uninstalling package

    ```
    Uninstall package <pkgname>;
    ```

    For package installation, the pkgName format is: <projectName\>.<packageName\>.`<projectName>.<packageName>`

-   View a package

    ```
        Show packages;
        View the list of packages already created and installed
        Describe package <pkgname>;
        View details of package
    
    ```

-   Client project grants access to package to other members or role of this project

    The installed package is an independent type of MaxCompute object. To access resources in a package \(resources shared with you by other projects\), you must have the permission to read package.

    If you do not have the Read permission, you must apply to the project owner or admin for the permission. The project owner or admin can grant permissions through ACL authorization or policy authorization.

    Authorize package to user or role:

    ```
    grant actions on package <pkgName> to user <username>;
    grant actions on package <pkgName> to role <role_name>;
    ```

    **Note:** After authorization, user has access to the object in that package only in this project.

    For example, the following ACL authorization allows the cloud account user odps\_test@aliyun.com to access resources in the package:

    ```
        use prj2;
        install package prj1.testpkg;
        grant read on package prj1.testpackage to user aliyun$odps_test@aliyun.com;
    ```

    \]

    Or allow all members of role role\_dev to access resources in package:

    ```
    use prj2;
        install package prj1.testpkg;
        grant read on package prj1.testpackage to role role_dev;
    ```


## Example {#section_hx1_3l1_wdb .section}

Jack is the administrator of prj1. John is the administrator of prj2. To address some business needs, Jack wants to share some resources of prj1 \(such as datamining.jar and sampletable\) to John's prj2. If prj2 user Bob must access these resources, the prj2 administrator can self-authorize Bob through ACL administrator or policy authorization without Jack’s involvement.

Procedure:

1.  Prj1 administrator Jack creates resources package in prj1.

    ```
        Use prj1;
        Create package datamicing; -- creating a package
        Add Resource dating. jar to package dating;-add resource to package
        Add Table sampletable to package dating; -- adding table to package
        Allow project prm9 to install package dating; -- sharing package to Project Space prm9
    ```

2.  Prj2 administrator Bob installs a package in prj2.

    ```
        use prj2;
        install package prj1.datamining; -- installs a package
        describe package prj1.datamining; -- view a list of resources in the package
    ```

3.  Bob self-authorizes the package.

    ```
        use prj2;
        grant Read on package prj1.datamining to user aliyun$bob@aliyun.com; -- authorization of Bob to use package via ACL
    ```


