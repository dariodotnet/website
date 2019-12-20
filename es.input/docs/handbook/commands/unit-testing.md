# Unit Testing

Lee http://kent-boogaart.com/blog/using-the-visual-studio-test-runner-for-mobile-development

No utilices un Moq ReactiveCommands.

`ReactiveCommand` está diseñado pensando en la pruebas. Además, la probabilidad de que suplante correctamente la semántica de `ReactiveCommand` a través de un Moq es bastante baja, es una clase bastante complicada (y si lo hiciera, terminaría haciendo un montón de trabajo innecesario). 
