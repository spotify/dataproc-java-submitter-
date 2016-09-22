# dataproc-java-submitter

A small java library for submitting Hadoop jobs to [Google Cloud Dataproc] from Java.

## Why?

In many real world usages of Hadoop, the jobs are usually parameterized to some degree.
Parameters can be anything from job configuration to input paths. It is common to resolve
these parameter arguments in some workflow tool that eventually puts the arguments on a
command line that is passed to the Hadoop job. On the job side, these arguments have to be
parsed using various tools that are more or less standard.

However if the argument resolution environment is in a JVM, dropping down to a shell and
invoking a command line can be pretty complicated and roundabout. It is also very limiting in
terms of what can be passed to the job. It is not uncommon to take more structured data and
store in some seralized format, stage the files, and have custom logic in the job to
deserialize it.

This library aims to more seamlessly bridge between a local JVM instance and the Hadoop
application entrypoint.

## RFC: Usage

```java
String project = "gcp-project-id";
String cluster = "dataproc-cluster-id";

DataprocHadoopRunner hadoopRunner = DataprocHadoopRunner.builder(project, cluster).build();
DataprocLambdaRunner lambdaRunner = DataprocLambdaRunner.forDataproc(hadoopRunner);

// Use any structured type that is Java Serializable
MyStructuredJobArguments arguments = resolveArgumentsInLocalJvm();

lambdaRunner.runOnCluster(() -> {

  // This lambda, including its closure will run on the Dataproc cluster
  System.out.println("Running on the cluster, with " + arguments.inputPaths());

  return 42; // rfc: is it worth supporting a return value from the job?
});
```

## Implementation

The current implementation is based on an internal Spotify project called Hydra. This
repository is currently empty as we're in the process of separating some of the internal
project from the reusable parts that are needed for this library.

[Google Cloud Dataproc]: https://cloud.google.com/dataproc/