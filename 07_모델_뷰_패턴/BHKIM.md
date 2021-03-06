# 7. 모델 뷰 패턴

1. 모델 뷰 패턴의 역사
2. MVC 패턴
3. MVP 패턴
4. MVVM 패턴

### MVC 패턴 (Model View Controller)
```js
class Model {
    constructor(name, description, outerWallThickness, numberOfTowers, moat) {
        this.name = name;
        this.description = description;
        this.outerWallThickness = outerWallThickness;
        this.numberOfTowers = numberOfTowers;
        this.moat = moat;
    }
}
class ValidationResult {
    constructor() {
        this.Errors = new Array();
    }
}
class CreateCastleView {
    constructor(document, controller, model, validationResult) {
        this.document = document;
        this.controller = controller;
        this.model = model;
        this.validationResult = validationResult;
        this.document.getElementById("saveButton").addEventListener("click", () => this.saveCastle());
        this.document.getElementById("castleName").value = model.name;
        this.document.getElementById("description").value = model.description;
        this.document.getElementById("outerWallThickness").value = model.outerWallThickness;
        this.document.getElementById("numberOfTowers").value = model.numberOfTowers;
        this.document.getElementById("moat").value = model.moat;
    }
    saveCastle() {
        var data = {
            name: this.document.getElementById("castleName").value,
            description: this.document.getElementById("description").value,
            outerWallThickness: this.document.getElementById("outerWallThickness").value,
            numberOfTowers: this.document.getElementById("numberOfTowers").value,
            moat: this.document.getElementById("moat").value
        };
        this.controller.saveCastle(data);
    }
}
class CreateCastleSuccess {
    constructor(document, controller, model) {
        this.document = document;
        this.controller = controller;
        this.model = model;
    }
}
class Controller {
    constructor(document) {
        this.document = document;
    }
    createCastle() {
        this.setView(new CreateCastleView(this.document, this));
    }
    saveCastle(data) {
        var validationResult = this.validate(data);
        if (validationResult.IsValid) {
            //save castle to storage
            this.saveCastleSuccess(data);
        }
        else {
            this.setView(new CreateCastleView(this.document, this, data, validationResult));
        }
    }
    saveCastleSuccess(data) {
        this.setView(new CreateCastleSuccess(this.document, this, data));
    }
    setView(view) {
        //send the view to the browser
    }
    validate(model) {
        var validationResult = new validationResult();
        if (!model.name || model.name === "") {
            validationResult.IsValid = false;
            validationResult.Errors.push("Name is required");
        }
        return;
    }
}
```

### MVP 패턴 (Model View Presenter)
```js
class CreateCastleModel {
    constructor(name, description, outerWallThickness, numberOfTowers, moat) {
        this.name = name;
        this.description = description;
        this.outerWallThickness = outerWallThickness;
        this.numberOfTowers = numberOfTowers;
        this.moat = moat;
    }
}
class ValidationResult {
    constructor() {
        this.Errors = new Array();
    }
}
class CreateCastleView {
    constructor(document, presenter) {
        this.document = document;
        this.presenter = presenter;
        this.document.getElementById("saveButton").addEventListener("click", this.saveCastle);
    }
    setCastleName(name) {
        this.document.getElementById("castleName").value = name;
    }
    getCastleName() {
        return this.document.getElementById("castleName").value;
    }
    setDescription(description) {
        this.document.getElementById("description").value = description;
    }
    getDescription() {
        return this.document.getElementById("description").value;
    }
    setOuterWallThickness(outerWallThickness) {
        this.document.getElementById("outerWallThickness").value = outerWallThickness;
    }
    getOuterWallThickness() {
        return this.document.getElementById("outerWallThickness").value;
    }
    setNumberOfTowers(numberOfTowers) {
        this.document.getElementById("numberOfTowers").value = numberOfTowers;
    }
    getNumberOfTowers() {
        return parseInt(this.document.getElementById("numberOfTowers").value);
    }
    setMoat(moat) {
        this.document.getElementById("moat").value = moat;
    }
    getMoat() {
        return this.document.getElementById("moat").value;
    }
    setValid(validationResult) {
    }
    saveCastle() {
        this.presenter.saveCastle();
    }
}
class CreateCastlePresenter {
    constructor(document) {
        this.document = document;
        this.model = new CreateCastleModel();
        this.view = new CreateCastleView(document, this);
    }
    saveCastle() {
        var data = {
            name: this.view.getCastleName(),
            description: this.view.getDescription(),
            outerWallThickness: this.view.getOuterWallThickness(),
            numberOfTowers: this.view.getNumberOfTowers(),
            moat: this.view.getMoat()
        };
        var validationResult = this.validate(data);
        if (validationResult.IsValid) {
            //write to the model
            this.saveCastleSuccess(data);
        }
        else {
            this.view.setValid(validationResult);
        }
    }
    saveCastleSuccess(data) {
        //redirect to different presenter
    }
    validate(model) {
        var validationResult = new validationResult();
        if (!model.name || model.name === "") {
            validationResult.IsValid = false;
            validationResult.Errors.push("Name is required");
        }
        return;
    }
}
```

### MVVM 패턴 (Model View ViewModel)
```js
class CreateCastleModel {
    constructor(name, description, outerWallThickness, numberOfTowers, moat) {
        this.name = name;
        this.description = description;
        this.outerWallThickness = outerWallThickness;
        this.numberOfTowers = numberOfTowers;
        this.moat = moat;
    }
}
class ValidationResult {
    constructor() {
        this.Errors = new Array();
    }
}
class CreateCastleView {
    constructor(document, viewModel) {
        this.document = document;
        this.viewModel = viewModel;
        this.document.getElementById("saveButton").addEventListener("click", () => this.saveCastle());
        this.document.getElementById("name").addEventListener("change", this.nameChangedInView);
        this.document.getElementById("description").addEventListener("change", this.descriptionChangedInView);
        this.document.getElementById("outerWallThickness").addEventListener("change", this.outerWallThicknessChangedInView);
        this.document.getElementById("numberOfTowers").addEventListener("change", this.numberOfTowersChangedInView);
        this.document.getElementById("moat").addEventListener("change", this.moatChangedInView);
    }
    nameChangedInView(name) {
        this.viewModel.nameChangedInView(name);
    }
    nameChangedInViewModel(name) {
        this.document.getElementById("name").value = name;
    }
    descriptionChangedInView(description) {
        this.viewModel.descriptionChangedInView(description);
    }
    descriptionChangedInViewModel(description) {
        this.document.getElementById("description").value = description;
    }
    numberOfTowersChangedInView(numberOfTowers) {
        this.viewModel.numberOfTowersChangedInView(numberOfTowers);
    }
    numberOfTowersChangedInViewModel(numberOfTowers) {
        this.document.getElementById("numberOfTowers").value = numberOfTowers;
    }
    outerWallThicknessChangedInView(outerWallThickness) {
        this.viewModel.outerWallThicknessChangedInView(outerWallThickness);
    }
    outerWallThicknessChangedInViewModel(outerWallThickness) {
        this.document.getElementById("outerWallThickness").value = outerWallThickness;
    }
    moatChangedInView(moat) {
        this.viewModel.moatChangedInView(moat);
    }
    moatChangedInViewModel(moat) {
        this.document.getElementById("moat").value = moat;
    }
    isValidChangedInViewModel(validationResult) {
        this.document.getElementById("validationWarning").innerHtml = validationResult.Errors;
        this.document.getElementById("validationWarning").className = "visible";
    }
    saveCastle() {
        this.viewModel.saveCastle();
    }
}
class CreateCastleViewModel {
    constructor(document) {
        this.document = document;
        this.model = new CreateCastleModel();
        this.view = new CreateCastleView(document, this);
    }
    nameChangedInView(name) {
        this.name = name;
    }
    nameChangedInViewModel(name) {
        this.view.nameChangedInViewModel(name);
    }
    descriptionChangedInView(description) {
        this.description = description;
    }
    descriptionChangedInViewModel(description) {
        this.view.descriptionChangedInViewModel(description);
    }
    outerWallThicknessChangedInView(outerWallThickness) {
        this.outerWallThickness = outerWallThickness;
    }
    outerWallThicknessChangedInViewModel(outerWallThickness) {
        this.view.outerWallThicknessChangedInViewModel(outerWallThickness);
    }
    numberOfTowersChangedInView(numberOfTowers) {
        this.numberOfTowers = numberOfTowers;
    }
    numberOfTowersChangedInViewModel(numberOfTowers) {
        this.view.numberOfTowersChangedInViewModel(numberOfTowers);
    }
    moatChangedInView(moat) {
        this.moat = moat;
    }
    moatChangedInViewModel(moat) {
        this.view.moatChangedInViewModel(moat);
    }
    saveCastle() {
        var validationResult = this.validate();
        if (validationResult.IsValid) {
            //write to the model
            this.saveCastleSuccess();
        }
        else {
            this.view.isValidChangedInViewModel(validationResult);
        }
    }
    saveCastleSuccess() {
        //do whatever is needed when save is successful.
        //Possibly update the view model
    }
    validate() {
        var validationResult = new validationResult();
        if (!this.name || this.name === "") {
            validationResult.IsValid = false;
            validationResult.Errors.push("Name is required");
        }
        return;
    }
}
```

### MVC vs MVP vs MVVM
![](./bhkim_/img1.png)
![](./bhkim_/img2.png)
Ref. [MVC, MVP and MVVM Design Patterns](https://www.slideshare.net/mudasirqazi00/design-patterns-mvc-mvp-and-mvvm)
