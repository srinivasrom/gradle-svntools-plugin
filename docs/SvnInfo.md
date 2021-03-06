## SvnInfo task (at.bxm.gradleplugins.svntools.tasks.SvnInfo)

Creates a `at.bxm.gradleplugins.svntools.api.SvnData` object (see [General Configuration](GeneralConfig.md)) that contains information about a file or directory
within an SVN workspace.
The object is added as an "extra property" to the Gradle project and may be accessed with `$project.svnData`.
This task requires the [standard SVN directory layout](http://svnbook.red-bean.com/en/1.7/svn.branchmerge.maint.html#svn.branchmerge.maint.layout) (`[module]/trunk`, `[module]/branches/[branch]`, `[module]/tags/[tag]`).

### Configuration

Property           | Description | Default value
------------------ | ----------- | -------------
sourcePath         | Source path for reading the SVN metadata | `$project.projectDir`
targetPropertyName | The name of the project extra property that will receive the resulting SvnData object | `svnData`
ignoreErrors       | Continue the build if the specified path doesn't contain SVN data | `false`
username           | The SVN username - leave empty if no authentication is required | `$project.svntools.username`
password           | The SVN password - leave empty if no authentication is required | `$project.svntools.password`

### Example

This Gradle script creates a `svn.properties` file that contains the SVN URL and revision of the buildfile, and adds it to the JAR artifact:

    apply plugin: "java"
    apply plugin: "at.bxm.svntools"

    task svnStatus(type: at.bxm.gradleplugins.svntools.tasks.SvnInfo) {
      sourcePath = project.buildFile
      doLast {
        def props = new Properties()
        props.setProperty("url", project.svnData.url)
        props.setProperty("revision", project.svnData.revisionNumber as String)
        file("$project.buildDir/svn.properties").withWriter { props.store(it, null) }
      }
    }

    jar {
      dependsOn svnStatus
      from(project.buildDir, { include "svn.properties" })
    }
