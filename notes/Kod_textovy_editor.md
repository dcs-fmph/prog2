---
title: Textový editor: kompletný zdrojový kód
---


``` Java
package editor;

import java.io.*;
import java.util.*;
import java.nio.file.*;
import javafx.application.*;
import javafx.stage.*;
import javafx.scene.*;
import javafx.scene.layout.*;
import javafx.scene.control.*;
import javafx.scene.text.*;
import javafx.scene.input.*;
import javafx.geometry.*;

public class Editor extends Application {

    private Stage primaryStage;
    private TextArea textArea;
    private FontDialog fontDialog;

    private Menu mFile;
    private Menu mFileNew;
    private MenuItem miFileNewEmpty;
    private MenuItem miFileNewRandom;
    private MenuItem miFileOpen;
    private MenuItem miFileSave;
    private MenuItem miFileSaveAs;
    private MenuItem miFileExit;
    private Menu mFormat;
    private MenuItem miFormatFont;
    private CheckMenuItem miFormatWrap;

    private ContextMenu contextMenu;
    private MenuItem cmiFormatFont;
    private CheckMenuItem cmiFormatWrap;

    private File openedFile;
    private boolean openedFileChanged;

    private String fontFamily = "Tahoma";
    private FontWeight fontWeight = FontWeight.NORMAL;
    private FontPosture fontPosture = FontPosture.REGULAR;
    private double fontSize = 16;

    private static final int N = 1000;  // V metode newRandomAction budeme generovat N riadkov o N nahodnych cifrach

    private void updateOpenedFileInformation(File file, boolean hasChanged) {
        openedFile = file;
        openedFileChanged = hasChanged;
        miFileSave.setDisable(!hasChanged);
        updatePrimaryStageTitle();
    }

    private void updatePrimaryStageTitle() {
        StringBuilder title = new StringBuilder("Textový editor (");
        if (openedFile == null) {
            title.append("neuložené v žiadnom súbore");
        } else {
            title.append(openedFile.getName());
        }
        title.append(")");
        if (openedFileChanged) {
            title.append("*");
        }
        primaryStage.setTitle(title.toString());
    }

    private void applyFont() {
        textArea.setFont(Font.font(fontFamily, fontWeight, fontPosture, fontSize));
    }

    private void handleTextAreaChange() {
        if (!openedFileChanged) {
            updateOpenedFileInformation(openedFile, true);
        }
    }

    private boolean saveBeforeClosingAlert() {
        if (openedFileChanged) {                                      // Ak dokument nie je ulozeny
            Alert alert = new Alert(Alert.AlertType.CONFIRMATION);    // Vytvor dialog typu Alert, variant CONFIRMATION
            alert.setTitle("Uložiť súbor?");                          // Nastav titulok dialogu
            alert.setHeaderText(null);                                // Dialog nebude mat ziaden "nadpis"
            alert.setContentText("Uložiť zmeny v súbore?");           // Nastav text dialogu

            ButtonType buttonTypeYes = new ButtonType("Áno");         // Vytvor instancie reprezentujuce typy tlacidiel
            ButtonType buttonTypeNo = new ButtonType("Nie");

            // Vdaka druhemu argumentu sa bude ako tlacidlo "Zrusit" spravat aj "krizik" v dialogovom okne vpravo hore:
            ButtonType buttonTypeCancel = new ButtonType("Zrušiť", ButtonBar.ButtonData.CANCEL_CLOSE);

            alert.getButtonTypes().setAll(buttonTypeYes, buttonTypeNo, buttonTypeCancel); // Nastav typy tlacidiel

            Optional<ButtonType> result = alert.showAndWait();        // Zobraz dialog a cakaj, kym sa zavrie
            if (result.get() == buttonTypeYes) {                      // Pokracuj podla typu stlaceneho tlacidla
                return saveAction();
            } else if (result.get() == buttonTypeNo) {
                return true;
            } else {
                return false;
            }
        } else {                                                      // Ak je dokument ulozeny, netreba robit nic
            return true;
        }
    }

    /**
     * Pomocna metoda, ktora vytvori dialog typu FileChooser a prednastavi niektore jeho vlastnosti.
     * @return Vytvoreny dialog typu FileChooser.
     */
    private FileChooser prepareFileChooser() {
        FileChooser fileChooser = new FileChooser();                                // Vytvorenie dialogoveho okna
        fileChooser.setInitialDirectory(new File(System.getProperty("user.dir")));  // Nastavenie vychodzieho adresara
        fileChooser.getExtensionFilters().addAll(                                   // Filtre na pripony
                new FileChooser.ExtensionFilter("Textové súbory (*.txt)", "*.txt"),
                new FileChooser.ExtensionFilter("Všetky súbory (*.*)", "*.*"));
        return fileChooser;
    }

    /**
     * Pomocna metoda realizujuca vyber suboru na otvorenie.
     * @return Vybrany subor alebo null v pripade, ze ziaden subor vybrany nebol.
     */
    private File chooseFileToOpen() {
        FileChooser fileChooser = prepareFileChooser();
        fileChooser.setTitle("Otvoriť");
        return fileChooser.showOpenDialog(primaryStage);
    }

    /**
     * Pomocna metoda realizujuca vyber suboru na ulozenie.
     * @return Vybrany subor alebo null v pripade, ze ziaden subor vybrany nebol.
     */
    private File chooseFileToSave() {
        FileChooser fileChooser = prepareFileChooser();
        fileChooser.setTitle("Uložiť");
        return fileChooser.showSaveDialog(primaryStage);
    }

    private String loadFromFile(File file) throws IOException {
        return Files.readString(Paths.get(file.getPath()));
    }

    private void writeToFile(File file, String s) throws IOException {
        Files.writeString(Paths.get(file.getPath()), s);
    }

    private boolean newEmptyAction() {
        if (saveBeforeClosingAlert()) {               // Pokracuj len ak sa podarilo zavriet dokument
            updateOpenedFileInformation(null, true);  // Nebude teraz otvoreny ziaden subor...
            textArea.clear();                         // Zmazeme obsah textoveho priestoru textArea
            return true;
        } else {
            return false;
        }
    }

    private boolean newRandomAction() {
        if (newEmptyAction()) {                           // Skus vytvorit novy subor a pokracuj len, ak sa to podarilo
            StringBuilder builder = new StringBuilder();  // Vygeneruj retazec o N x N nahodnych cifrach
            Random random = new Random();
            for (int i = 1; i <= N; i++) {
                for (int j = 1; j <= N; j++) {
                    builder.append(random.nextInt(10));
                }
                builder.append(System.lineSeparator());
            }
            textArea.setText(builder.toString());         // Vypis vygenerovany retazec do textoveho priestoru textArea
            return true;
        } else {
            return false;
        }
    }

    private boolean openAction() {
        if (saveBeforeClosingAlert()) {                        // Ak sa podarilo zavriet dokument
            File file = chooseFileToOpen();                    // Vyber subor na otvorenie
            if (file != null) {                                // Ak bol nejaky subor vybrany ...
                try {
                    textArea.setText(loadFromFile(file));      // ... vypis jeho obsah do textArea
                    updateOpenedFileInformation(file, false);  // Aktualizuj informacie o otvorenom subore
                } catch (IOException e) {
                    System.err.println("Nieco sa pokazilo.");
                }
                return true;
            } else {
                return false;
            }
        } else {
            return false;
        }
    }

    private boolean saveAction() {
        if (openedFile == null) {                                // Ak nebol otvoreny ziaden subor ...
            return saveAsAction();                               // ... realizuj to iste ako pri "Ulozit ako"
        } else {                                                 // V opacnom pripade ...
            try {
                writeToFile(openedFile, textArea.getText());     // ... prepis aktualne otvoreny subor
                updateOpenedFileInformation(openedFile, false);  // Aktualizuj informacie o otvorenom subore
            } catch (IOException e) {
                System.err.println("Nieco sa pokazilo.");
            }
            return true;
        }
    }

    private boolean saveAsAction() {
        File file = chooseFileToSave();                    // Vyber subor, do ktoreho sa ma ukladat
        if (file != null) {                                // Ak bol nejaky subor vybrany ...
            try {
                writeToFile(file, textArea.getText());     // ... zapis don obsah textArea
                updateOpenedFileInformation(file, false);  // Aktualizuj informacie o otvorenom subore
            } catch (IOException e) {
                System.err.println("Nieco sa pokazilo.");
            }
            return true;
        } else {
            return false;
        }
    }

    private boolean exitAction() {
        if (saveBeforeClosingAlert()) {   // Pokracuj len ak sa podarilo zavriet dokument
            Platform.exit();              // Ukonci aplikaciu
            return true;
        } else {
            return false;
        }
    }

    private boolean fontAction() {
        FontAttributes fontAttributes = fontDialog.showFontDialog(
                new FontAttributes(fontFamily, fontWeight, fontPosture, fontSize));
        if (fontAttributes != null) {
            fontFamily = fontAttributes.getFamily();
            fontWeight = fontAttributes.getWeight();
            fontPosture = fontAttributes.getPosture();
            fontSize = fontAttributes.getSize();
            applyFont();
        }
        return true;
    }

    private boolean handleStageCloseRequest(WindowEvent event) {
        if (saveBeforeClosingAlert()) {  // Ak sa podarilo zavriet subor
            return true;
        } else {                         // Ak sa nepodarilo zavriet subor ...
            event.consume();             // ... nechceme ani zavriet okno
            return false;
        }
    }

    public void start(Stage primaryStage) {
        this.primaryStage = primaryStage;

        BorderPane borderPane = new BorderPane();
        Scene scene = new Scene(borderPane);

        textArea = new TextArea();
        borderPane.setCenter(textArea);
        textArea.setPadding(new Insets(5, 5, 5, 5));
        textArea.setPrefWidth(1000);
        textArea.setPrefHeight(700);
        applyFont();
        textArea.textProperty().addListener((observable, oldValue, newValue) -> handleTextAreaChange());

        MenuBar menuBar = new MenuBar();
        borderPane.setTop(menuBar);

        mFile = new Menu("Súbor");
        mFileNew = new Menu("Nový");
        miFileNewEmpty = new MenuItem("Prázdny súbor");
        miFileNewRandom = new MenuItem("Náhodné cifry");
        mFileNew.getItems().addAll(miFileNewEmpty, miFileNewRandom);
        miFileOpen = new MenuItem("Otvoriť...");
        miFileSave = new MenuItem("Uložiť");
        miFileSaveAs = new MenuItem("Uložiť ako...");
        miFileExit = new MenuItem("Koniec");
        mFile.getItems().addAll(mFileNew, miFileOpen, miFileSave, miFileSaveAs, new SeparatorMenuItem(), miFileExit);

        mFormat = new Menu("Formát");
        miFormatFont = new MenuItem("Písmo...");
        miFormatWrap = new CheckMenuItem("Zalamovať riadky");
        miFormatWrap.setSelected(false);                        // Nie je nutne, kedze false je tu vychodzia hodnota
        mFormat.getItems().addAll(miFormatFont, miFormatWrap);

        menuBar.getMenus().addAll(mFile, mFormat);

        miFileNewEmpty.setAccelerator(new KeyCodeCombination(KeyCode.N, KeyCombination.CONTROL_DOWN)); // Ctrl + N
        miFileOpen.setAccelerator(new KeyCodeCombination(KeyCode.O, KeyCombination.CONTROL_DOWN));     // Ctrl + O
        miFileSave.setAccelerator(new KeyCodeCombination(KeyCode.S, KeyCombination.CONTROL_DOWN));     // Ctrl + S

        miFileNewEmpty.setOnAction(event -> newEmptyAction());
        miFileNewRandom.setOnAction(event -> newRandomAction());
        miFileOpen.setOnAction(event -> openAction());
        miFileSave.setOnAction(event -> saveAction());
        miFileSaveAs.setOnAction(event -> saveAsAction());
        miFileExit.setOnAction(event -> exitAction());
        miFormatFont.setOnAction(event -> fontAction());

        contextMenu = new ContextMenu();
        textArea.setContextMenu(contextMenu);
        cmiFormatFont = new MenuItem("Formát písma...");
        cmiFormatWrap = new CheckMenuItem("Zalamovať riadky");
        cmiFormatWrap.setSelected(false);
        contextMenu.getItems().addAll(cmiFormatFont, cmiFormatWrap);

        cmiFormatFont.setOnAction(event -> fontAction());

        textArea.wrapTextProperty().bindBidirectional(miFormatWrap.selectedProperty());
        textArea.wrapTextProperty().bindBidirectional(cmiFormatWrap.selectedProperty());

        fontDialog = new FontDialog();

        updateOpenedFileInformation(null, true);

        primaryStage.setOnCloseRequest(event -> handleStageCloseRequest(event));
        primaryStage.setScene(scene);
        primaryStage.show();
    }

    public static void main(String[] args) {
        launch(args);
    }
}
```

``` Java
package editor;

import javafx.scene.text.*;

public class FontAttributes {
    private String family;
    private FontWeight weight;
    private FontPosture posture;
    private double size;

    public String getFamily() {
        return family;
    }

    public FontWeight getWeight() {
        return weight;
    }

    public FontPosture getPosture() {
        return posture;
    }

    public double getSize() {
        return size;
    }

    public FontAttributes(String family, FontWeight weight, FontPosture posture, double size) {
        this.family = family;
        this.weight = weight;
        this.posture = posture;
        this.size = size;
    }
}
```

``` Java
package editor;

import java.util.*;
import javafx.stage.*;
import javafx.scene.*;
import javafx.scene.layout.*;
import javafx.scene.control.*;
import javafx.scene.text.*;
import javafx.geometry.*;
import javafx.collections.*;

public class FontDialog {
    private Stage stage;

    private ListView<String> lvFamilies;
    private ArrayList<RadioButton> rbSizes;
    private RadioButton rbRegular;
    private RadioButton rbBold;
    private RadioButton rbItalic;
    private RadioButton rbBoldItalic;

    private ToggleGroup toggleGroup1;
    private ToggleGroup toggleGroup2;

    private boolean confirmed;

    private void okAction() {
        confirmed = true;
        stage.close();
    }

    private void cancelAction() {
        stage.close();
    }

    public FontDialog() {
        BorderPane borderPane = new BorderPane();
        Scene scene = new Scene(borderPane);

        GridPane grid = new GridPane();
        borderPane.setCenter(grid);
        grid.setPadding(new Insets(10, 15, 10, 15));
        grid.setAlignment(Pos.CENTER);
        grid.setHgap(10);

        lvFamilies = new ListView<>();
        grid.add(lvFamilies, 0, 0);
        lvFamilies.setItems(FXCollections.observableList(Font.getFamilies()));
        lvFamilies.setPrefHeight(200);

        toggleGroup1 = new ToggleGroup();
        rbSizes = new ArrayList<>();
        for (int i = 8; i <= 24; i += 2) {
            RadioButton rb = new RadioButton(Integer.toString(i));
            rb.setToggleGroup(toggleGroup1);
            rbSizes.add(rb);
        }

        VBox rbBox1 = new VBox();
        rbBox1.setSpacing(10);
        rbBox1.setPadding(new Insets(0, 20, 0, 10));
        rbBox1.getChildren().addAll(rbSizes);
        grid.add(rbBox1, 1, 0);

        toggleGroup2 = new ToggleGroup();

        rbRegular = new RadioButton("Obyčajný");
        rbBold = new RadioButton("Tučný");
        rbItalic = new RadioButton("Kurzíva");
        rbBoldItalic = new RadioButton("Tučná kurzíva");

        rbRegular.setToggleGroup(toggleGroup2);
        rbBold.setToggleGroup(toggleGroup2);
        rbItalic.setToggleGroup(toggleGroup2);
        rbBoldItalic.setToggleGroup(toggleGroup2);

        VBox rbBox2 = new VBox();
        rbBox2.setSpacing(20);
        rbBox2.setPadding(new Insets(0, 10, 0, 20));
        rbBox2.getChildren().addAll(rbRegular, rbBold, rbItalic, rbBoldItalic);
        grid.add(rbBox2, 2, 0);

        TilePane bottom = new TilePane();
        borderPane.setBottom(bottom);
        bottom.setPadding(new Insets(15, 15, 15, 15));
        bottom.setAlignment(Pos.BASELINE_RIGHT);
        bottom.setHgap(10);

        Button btnOK = new Button("Potvrdiť");
        btnOK.setMaxWidth(Double.MAX_VALUE);
        Button btnCancel = new Button("Zrušiť");
        btnCancel.setMaxWidth(Double.MAX_VALUE);
        bottom.getChildren().addAll(btnOK, btnCancel);

        btnOK.setOnAction(event -> okAction());
        btnCancel.setOnAction(event -> cancelAction());

        stage = new Stage();
        stage.initStyle(StageStyle.UTILITY);             // Prejavi sa v systemovych ikonach okna
        stage.initModality(Modality.APPLICATION_MODAL);  // Pocas zobrazenia okna sa nebude dat pristupovat k ostatnym oknam
        stage.setScene(scene);
        stage.setTitle("Formát písma");
    }

    /**
     * Metoda, ktora zobrazi dialogove okno stage, pricom vychodzie nastavenie ovladacich prvkov bude urcene parametrom
     * oldFontAttributes. Po ukonceni dialogu vrati atributy fontu vybrane pouzivatelom.
     * @param oldFontAttributes Atributy fontu, ktorym sa text zobrazoval doposial. Na zaklade nich sa nastavia
     *                          vlastnosti zobrazovanych ovladacich prvkov dialogu.
     * @return                  Nova instancia triedy FontAttributes reprezentujuca atributy fontu vybraneho v dialogu
     *                          pouzivatelom. V pripade ukoncenia dialogu inym sposobom, nez potvrdenim, je navratovou
     *                          hodnotou metody referencia null.
     */
    public FontAttributes showFontDialog(FontAttributes oldFontAttributes) {
        /* Nastav predvoleny font podla oldFontAttributes: */

        lvFamilies.getSelectionModel().select(oldFontAttributes.getFamily());
        rbSizes.get((int)((oldFontAttributes.getSize() - 8) / 2)).setSelected(true);
        if (oldFontAttributes.getWeight() == FontWeight.NORMAL) {
            if (oldFontAttributes.getPosture() == FontPosture.REGULAR) {
                rbRegular.setSelected(true);
            } else if (oldFontAttributes.getPosture() == FontPosture.ITALIC) {
                rbItalic.setSelected(true);
            }
        } else if (oldFontAttributes.getWeight() == FontWeight.BOLD) {
            if (oldFontAttributes.getPosture() == FontPosture.REGULAR) {
                rbBold.setSelected(true);
            } else if (oldFontAttributes.getPosture() == FontPosture.ITALIC) {
                rbBoldItalic.setSelected(true);
            }
        }

        /* Otvor dialogove okno: */

        confirmed = false;
        stage.showAndWait();

        /* Ak dialog skoncil potvrdenim, vrat zvolene hodnoty na vystupe: */

        if (confirmed) {
            String newFamily = "";
            FontWeight newWeight;
            FontPosture newPosture;
            double newSize = 0;
            newFamily = lvFamilies.getSelectionModel().getSelectedItem();
            if (rbRegular.isSelected() || rbItalic.isSelected()) {
                newWeight = FontWeight.NORMAL;
            } else {
                newWeight = FontWeight.BOLD;
            }
            if (rbRegular.isSelected() || rbBold.isSelected()) {
                newPosture = FontPosture.REGULAR;
            } else {
                newPosture = FontPosture.ITALIC;
            }
            int i = 8;
            for (RadioButton rb : rbSizes) {
                if (rb.isSelected()) {
                    newSize = i;
                }
                i += 2;
            }
            return new FontAttributes(newFamily, newWeight, newPosture, newSize);
        } else {
            return null;
        }
    }
}
```

