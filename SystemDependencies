import com.sun.media.sound.SoftAbstractResampler;

import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;
import java.util.Objects;

public class SoftwareDependency {

    enum COMMAND {DEPEND, INSTALL, REMOVE, LIST, END}

    static class Software {

        private String name;
        private List<Software> dependencies;

        Software(String name) {
            this.name = name;
            dependencies = new ArrayList<>();
        }

        String getName() {
            return name;
        }

        List<Software> getDependencies() {
            return dependencies;
        }


        void addDependencies(Software dependency) {
            this.dependencies.add(dependency);
        }

        @Override
        public String toString() {
            return  this.name;
        }

        @Override
        public boolean equals(Object o) {
            if (this == o) {
                return true;
            }
            if (!(o instanceof Software)) {
                return false;
            }
            Software software = (Software) o;
            return Objects.equals(name, software.name);
        }

        @Override
        public int hashCode() {
            return Objects.hash(name);
        }
    }

    private static Map<String, Software> allSoftware = new HashMap<>();

    private static List<Software> installedSoftware = new ArrayList<>();


    static void doIt(String[] input) {

        for (String inp : input) {
            System.out.println(inp);

            final String[] commandTokens = inp.split("\\s+");
            final String commandString = commandTokens[0];

            final COMMAND command = COMMAND.valueOf(commandString);
            switch (command) {
                case DEPEND:
                    final String softwareName = commandTokens[1];
                    buildDependencies(softwareName, commandTokens);
                    break;
                case INSTALL:
                    final Software softwareToBeInstalled = getSoftware(commandTokens[1]);
                    if (isAlreadyInstalled(softwareToBeInstalled)) {
                        System.out.println(softwareToBeInstalled + " is already installed");
                    } else {
                        //Install dependencies first
                        final List<Software> softwareDependenciesToBeInstalled = softwareToBeInstalled.getDependencies();
                        for (Software softwareDependency : softwareDependenciesToBeInstalled) {
                            if (!isAlreadyInstalled(softwareDependency)) {
                                install(softwareDependency);
                            }
                        }

                        //Once the dependencies are installed, install the software
                        install(softwareToBeInstalled);
                    }
                    break;
                case REMOVE:
                    final Software softwareToBeRemoved = getSoftware(commandTokens[1]);
                    if (!isAlreadyInstalled(softwareToBeRemoved)) {
                        System.out.println(softwareToBeRemoved + " is not installed");
                    }
                    else if (canRemoveSoftware(softwareToBeRemoved)) {
                        System.out.println("Removing " + softwareToBeRemoved);
                        installedSoftware.remove(softwareToBeRemoved);

                        final List<Software> currentSoftwareDependencies = softwareToBeRemoved.getDependencies();
                        for (Software dependency : currentSoftwareDependencies) {
                            if (canRemoveSoftware(dependency)) {
                                System.out.println("Removing " + dependency);
                                installedSoftware.remove(dependency);
                            }
                        }
                    } else {
                        System.out.println(softwareToBeRemoved + " is still needed");
                    }
                    break;
                case LIST:
                    for (Software installedSoftware : installedSoftware) {
                        System.out.println(installedSoftware);
                    }
                    break;
                case END:
                    break;
            }
        }

    }

    private static void install(Software software) {
        for (Software dependentSoftware: getSoftware(software.name).getDependencies()) {
            if (!installedSoftware.contains(dependentSoftware))
                System.out.println("Installing " + dependentSoftware);
        }
        System.out.println("Installing " + software);
        installedSoftware.add(software);
    }

    private static boolean isAlreadyInstalled(Software softwareToBeInstalled) {
        return installedSoftware.contains(softwareToBeInstalled);
    }

    private static void buildDependencies(String softwareName, String[] commandTokens) {
        //The dependencies of the current command are available from 3rd position onwards
        for (int i = commandTokens.length - 1; i > 1; i--) {
            final String currentDependency = commandTokens[i];
            final List<Software> dependenciesOfdependency = getSoftware(currentDependency).getDependencies();

            if (dependenciesOfdependency != null && dependenciesOfdependency.contains(getSoftware(softwareName))) {
                System.out.println(currentDependency + " depends on " + softwareName + ", ignoring command");
            } else {
                getSoftware(softwareName).addDependencies(getSoftware(currentDependency));
            }
        }
    }

    private static Software getSoftware(String name) {
        Software software = allSoftware.get(name);
        if (software == null) {
            software = new Software(name);
            allSoftware.put(name, software);
        }
        return software;
    }

    private static boolean canRemoveSoftware(Software softwareToBeRemoved) {
        for (Software installedSoftware : installedSoftware) {
            final List<Software> requiredDependencies = installedSoftware.getDependencies();
            if (requiredDependencies != null) {
                for (Software dependency : requiredDependencies) {
                    if (softwareToBeRemoved.equals(dependency)) {
                        return false;
                    }
                }
            }
        }
        return true;
    }

    public static void main(String[] args){
        String[] inp = new String[]{"DEPEND A B C",
                "DEPEND B D",
                "INSTALL A",
                "END"};

        doIt(inp);
    }
}
