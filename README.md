# The Alfresco Map 

## Organization of the Map 

The Map is comprised of [markdown](https://github.com/gitlabhq/gitlabhq/blob/master/doc/user/markdown.md) pages containing Articles that describe Alfresco Components and Design Artifacts that help explain, pictorially, some of the details in the articles.  

The logical organization of the pages looks like this.
```
/map
    /system/
       /<subsystem-name>
          /<component-name>
```
Each level in the folder hierarchy contains ...
* A **README.md** file containing information about that level of the Map
* Zero or more **other markdown files** that provide information referenced from the README.md
* A **resource** folder that containts Design Artifacts, such as images, that that provide information referenced from the markdown files

The Home Page for the system is found [here](./system)  

The Contributor Guide is found [here](./contributor-guide.md)

