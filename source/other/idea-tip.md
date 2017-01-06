# Tips for IntelliJ IDEA
## Setting

### 1. Show line numbers
Editor->General->Appearance->`Show line numbers`.

### 2. Import all packages
Editor->General->Auto Import->`Add unambiguous import on the fly`.

After enable above setting,you can import all packages when using hot key `alt+enter`

### 3. Optimize packages
Editor->General->Auto Import->`Optimize import on the fly`.

After enable above setting,when you delete some codes ,import statements brought by those codes will be removed automatically.

### 4. Avoid  import *
Editor->Code Style->Java->`Class count to user import with *`.

You can set a number which is suitable for you.

### 5. Prevent opening last projects when intellij idea start
Settings -> Appearance & Behaviour -> System Settings -> Reopen last project on startup

### 6. Support ES6
File>settings>Lauguages & Frameworks>javascript
change `javascript languages` version to `ECMAScript 6`。

## Hot keys

All hot keys are defined on the keymaps of Eclipse which you can set it by **`Settings->Keymap`**.

### 1. Ctrl + Shift + k

Push changes to git remote repository.

It is equivalent to **`right-mouse->git->repository->push`**.

### 2. Alt + Shift + r

do rename operation.

It is equivalent to **`right-mouse->refactor->rename`**.

### 3. Ctrl + g

Finding all places where a particular class, method or variable is used in the whole project,
but different with `Ctrl + Alt + F7`,it show you the same information in a tool window at the bottom that will stay there.
will 

### 4. Ctrl + Alt + F7

Finding all places where a particular class, method or variable is used in the whole project,
it gives you a nice popup with good interaction.