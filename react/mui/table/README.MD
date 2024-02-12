## Create react apps project with MUI table

1. Create a React App: <br> Within the same project directory, create a React app using Create React App:
    ```shell
   npx create-react-app frontend --template typescript
    ```
2. Change directory to the frontend folder.

3. Run one of the following commands to add Material UI to your project:<br>
   [MUI Installation doc](https://mui.com/material-ui/getting-started/installation/)
    ```shell
    npm install @mui/material @emotion/react @emotion/styled
    ```

4. Material UI uses the Roboto font by default. Add it to
   your project via Fontsource, or with the Google Fonts CDN.
    ```shell
    npm install @fontsource/roboto
    ```
   Then you can import it in your entry point like this:
    ```ts {1,3}
    import '@fontsource/roboto/300.css';
    import '@fontsource/roboto/400.css';
    import '@fontsource/roboto/500.css';
    import '@fontsource/roboto/700.css';
    ```

5. To use the font Icon component or the prebuilt SVG Material Icons (such as those found in the icon demos),
   you must first install the Material Icons font. You can do so with npm, or with the Google Web Fonts CDN.
    ```shell
    npm install @mui/icons-material
    ```
6. Create directory frontend/src/components


