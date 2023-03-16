**POROJECT 13 - ANSIBLE DYNAMIC ASSIGNMENTS (INCLUDE) AND COMMUNITY ROLES**

---

Ansible is an actively developing software project, so you are encouraged to visit `Ansible` Documentation for the latest updates on modules and their usage.

The last Projects gave us hands on `Ansible` experience on how to perform configurations using `playbooks`, `roles` and `imports`.

**Project 13** will introduce *dynamic assignments* by using `include` module. From Project 12, we can  tell that *static assignments* use `import` Ansible module. The module that enables *dynamic assignments* is `include`.

    ```
    import = Static
    include = Dynamic
    ```
When the `import` module is used, all statements are pre-processed at the time playbooks are **parsed**. Meaning, when you execute `site.yml` playbook, Ansible will process all the playbooks referenced during the time it is parsing the statements. This also means that, during actual execution, if any statement changes, such statements will not be considered. Hence, it is `static`.

On the other hand, when `include` module is used, all statements are processed only during execution of the playbook. Meaning, after the statements are **parsed**, any changes to the statements encountered during execution will be used.

In most cases it is recommended to use `static` assignments for playbooks, because it is more reliable. With `dynamic` ones, it is hard to debug playbook problems due to its `dynamic` nature. However, you can use `dynamic` assignments for environment specific variables as we will be introducing in this project.