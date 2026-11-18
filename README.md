# Jenner-Analytics-handy-little-macro-with-inline-python-to-convert-any-text-file-to-a-pdf
Jenner Analytics handy little macro with inline python to convert any text file to a pdf

    /*---
    c:/utl/jnr-jenner-analytics-handy-little-macro-with-inline-python-to-convert-any-text-file-to-a-pdf
    ---*/

    Jenner Analytics handy little macro with inline python to convert any text file to a pdf

    NOTE: SAS and Siemens SLC do not support Pythoninside a macro and
          do not support macro trigger resolution inside a inline python.

    CONTENTS

      1.  Input
      2.  Process
      3.  Output
      4.  Macro

    Too long to post: see
    https://tinyurl.com/5dzfdyek
    https://github.com/rogerjdeangelis/Jenner-Analytics-handy-little-macro-with-inline-python-to-convert-any-text-file-to-a-pdf

    /**************************************************************************************************************************/
    /* 1 INPUT                                                                                                                */
    /**************************************************************************************************************************/

    filename ft15f001 "d:/txt/adsl.txt";
    parmcards4;
    proc format lib=library;
     value
       /     Major   Page Line             Major    Minor      Staistic     Process
                     2     3   4      5    6        7          8            9
       1  = "AGE     @Page @01 @Order @110 @AGE (y) @N         @N           @MEANS"
       2  = "AGE     @Page @01 @Order @120 @AGE (y) @Mean(SD)  @Mean StdDev @MEANS"
       3  = "AGE     @Page @01 @Order @130 @AGE (y) @Median    @Median      @MEANS"
       4  = "AGE     @Page @01 @Order @140 @AGE (y) @Min, Max  @Min Max     @MEANS";
     value AGEGR1N
       2  = "AGEGR1N @Page @01 @Order @210 @AGE (y) @<65 yrs   @NPCT        @NPCT"
       1  = "AGEGR1N @Page @01 @Order @220 @AGE (y) @65-80 yrs @NPCT        @NPCT"
       3  = "AGEGR1N @Page @01 @Order @230 @AGE (y) @>80       @NPCT        @NPCT";
    ;;;;
    run;

    /*---
    d:/txt/adsl.txt
    https://github.com/rogerjdeangelis/Jenner-Analytics-handy-little-macro-with-inline-python-to-convert-any-text-file-to-a-pdf/blob/main/adsl.txt
    ---*/
    
    /**************************************************************************************************************************/
    /* 2 PROCESS                                                                                                              */
    /**************************************************************************************************************************/

    %jnr_toPDF(
        input ="d:/txt/adsl.txt"
       ,output="d:/pdf/adsl.pdf"
       );

    /**************************************************************************************************************************/
    /* 3 OUTPUT                                                                                                               */
    /**************************************************************************************************************************/

    /*---
    d:/pdf/adsl.pdf
    https://github.com/rogerjdeangelis/Jenner-Analytics-handy-little-macro-with-inline-python-to-convert-any-text-file-to-a-pdf/blob/main/adsl.pdf
    ---*/
    
    /**************************************************************************************************************************/
    /* 4 MACROT                                                                                                               */
    /**************************************************************************************************************************/

    filename ft15f001 "c:/otojnr/jnr_toPDF.sas";
    parmcards4;
    %macro jnr_toPDF(
        input ="d:/txt/adslfix.txt"
       ,output="d:/pdf/adslfix.pdf"
    );

    options set=PROC_PYPATH "D:\py314\python.exe";

    proc python;
    submit;
    from pathlib import Path
    from fpdf import FPDF

    input_file  = Path(&input)
    output_file = Path(&output)

    if input_file.resolve() == output_file.resolve():
        raise ValueError("Input and output must be different files.")

    text = input_file.read_text(encoding="utf-8")

    output_file.parent.mkdir(parents=True, exist_ok=True)

    pdf = FPDF(orientation="L", unit="mm", format="Letter")
    pdf.set_margins(left=12, top=12, right=12)
    pdf.set_auto_page_break(auto=True, margin=12)
    pdf.set_font("Courier", size=9)

    for page_text in text.expandtabs(8).split("\f"):
        pdf.add_page()

        pdf.multi_cell(
            w=0,
            h=4,
            text=page_text,
            align="L",
            new_x="LMARGIN",
            new_y="NEXT"
        )

    pdf.output(str(output_file))

    print(f"Created: {output_file}")
    print(f"Size: {output_file.stat().st_size:,} bytes")
    endsubmit;
    run;

    %mend jnr_toPDF;
    ;;;;
    run;

    /*--- END ---*/


    
