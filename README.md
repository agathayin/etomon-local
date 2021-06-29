# @etomon/etomon-locale

Etomon-locale is based on i18next. Please check [i18next documentation](https://www.i18next.com/) for any i18n questions. In this documentation, there are 2 parts. The first will describe how @etomon-locale is structured and how front end uses it so that developer knows how to find the function and files when debugging. The second part shows how to use @etomon-locale and add translation onto pages.



### etomon-locale structure

> etomon-local 
> > **lib**: compiled files of the settings in /src   
> > **locales**: includes folders for different languages. Inside language folders, file name for components or pages should be the same.  
> > > en  
> > > > components: contains translation for React components such as timeslotList, search, and etc.  
> > > > pages: contains translation for pages such as courseDetail, courseListing, and etc.  
> > > > 
> > > zh  
> > > > components  
> > > > pages
> > > >   
> > **outDist**: the final output of translation for Etomon frontend to read.  
> > **src**: basic setting for js and React. `index.js` contains the function generating the final json file in outDist.  
> > **loadLanguage.js**: set which language the page will show  
> > **jquery-inject.js**: init i18n and set language, used for ejs. The page will consistently load translation for `[data-i18n]`. After finding content, `data-etomon-locale` will become "1" and page stop loading translation. 


### how frontend uses @etomon-locale

in `/config/defaultConfig.js`,clientSideLibs.libs imports @etomon-locale and fetches the default language data. If you want to check the translation content used on frontend, go to <https://etomon.com/lib/js/etomon-locale-en> or <https://etomon.com/lib/js/etomon-locale-zh> and you will be able to see the translation object as in outDist.

in `search-client/src/search`, `require('@etomon/etomon-locale/jquery-inject').initTranslation();` calls the initTranslation and fetch translation for all jQuery/ejs pages.

***

### how to add/edit translation and to publish

1. change files in `/locales`
2. run `npm run prepare` to generate files in `/outDist`. Make sure `/outDist/en.json` and `/outDist/zh.json` have the latest translation as in `/locales`.
3. change version, push changes into [etomon-locale master branch](https://bitbucket.org/etomon/etomon-locale/branch/master) and `npm publish`.
4. (wait for about 10 minutes), use `npm upgrade @etomon/etomon-locale` to upgrade library to the latest version.

### add translation for jQuery pages

add `data-i18n` as div attribute. For example:
```
<div data-18n="pages.courseRecord.title">title</div>
<div class="passcode">
	<span data-i18n="pages.courseRecord.tab-one.info.text-two">Passcode: </span>
	<span rel="zoomPasscode"></span>
</div>
    
```

### add translation for React pages or components

1. import i18n in `index.js` 
```
import { initI18n } from '@etomon/etomon-locale';
import { I18nextProvider } from "react-i18next";
```
2. wrap App (the top component for the whole page) with I18nextProvider in `index.js`
```
const app=(
  <I18nextProvider i18n={i18n}>
    <App />
  </I18nextProvider>
);
```
3. in component file (eg. App.js, CourseMission.js ), import Trans and withTranslation
```
import { Trans ,withTranslation } from "react-i18next";
```
4. add translation  
	- add translation.   
	``` 
    //What really matters is the content in Trans. 
    //The comment "Go Back" is only for helping developers to find codes.
    <a className="goBack" href="/courses/listing">{/* Go Back */}
    	<Trans>pages.courseDetail.preview.button-one</Trans>
    </a>
    ``` 
	- add translation with html tags with `components={[<div>...</div>,<div>...</div>]}`
	
    *in this case, translation must be wrapped by number e.g. `<0>...</0>...<1>...</1>`*
	
	```
    <div><Trans components={[<b>child</b>]}>pages.courseDetail.preview.title</Trans></div>
    ```  
    - add translation with variable
    ```
    <Trans values={{price:Number(data.activePrice)}}>pages.payment.process.details.item.price</Trans>
    ```
    - pass i18n to child component. By passing i18n as props or using withTranslation, `this.props.t` contains the i18next functions.
    ```
    <EtomonTimeslotList lessonId={this.state.course.defaultLesson} sticky={true} i18n={this.props.i18n} />
    ```
    - use this.props.t to translate
    ```
    const { t, i18n } = this.props;
    t('pages.editProfile.basicinfo.photo.alert')
    ```


5. export component with `withTranslation` or use `props.t`
```
export default withTranslation()(App);
```
```
this.props.t(<Tran>pages.courseDetail.title</Trans>)
```
