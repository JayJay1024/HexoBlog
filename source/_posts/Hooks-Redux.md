---
title: Hooks Redux
date: 2021-01-13 11:18:41
tags: [Redux]
categories: 攻城狮那些事儿
---

## Top Level File

```tsx
import { Provider } from 'react-redux';
import store from '<path of store file>';

export type RootState = ReturnType<typeof store.getState>;

<Provider store={store}>
    <App />
</Provider>
```

<!--more-->

## Store File

```ts
import reduxSliceOne from '<redux slice file one>';
import reduxSliceTwo from '<redux slice file two>';

export default configureStore({
    reducer: {
        reduxOne: reduxSliceOne,
        reduxTwo: reduxSliceTwo,
    },
});
```

## Redux Slice File One

```ts
import { createSlice, PayloadAction, createAsyncThunk } from '@reduxjs/toolkit';

import { RootState } from '<path of top level file>';

type DataState = string;  // What do u want

interface ReduxSliceOneState {
    data: DataState,
    status: 'idle' | 'loading' | 'succeeded' | 'failed',
    error: string | null | undefined,
}

const initialState = {
    data: 'Hey Man ~',
    status: 'idle',
    error: null,
} as ReduxSliceOneState;

interface fetchParams {
    id: number,
    age: number,
}

// Async Update
const fetchReduxOneData = createAsyncThunk(
    'redux/one',  // What do u want
    async (params: fetchParams) => {

        // Just for example

        await fetch(params);

        // Or:
        // return new Promise<DataState>((resolve, rejects) => {
        //     const client = new Client('<url>');

        //     client.on('error', async (err) => {
        //         rejects(err);
        //     });
        //     client.on('close', () => {});

        //     client.on("open", async () => {
        //         try {
        //             const data = await client.fetch(params);
        //             resolve(data);
        //         } catch (err) {
        //             rejects(err)
        //         } finally {
        //             client.close();
        //         }
        //     });
        // });
    }
);

const slice = createSlice({
    name: 'reduxOne',
    initialState: initialState,
    reducers: {
        // Sync Update
        updateData(state, action: PayloadAction<DataState>) {
            state.data = action.payload;
        },
    },
    extraReducers: builder => {
        builder.addCase(fetchReduxOneData.pending, (state) => {
            state.status = 'loading';
        });
        builder.addCase(fetchReduxOneData.rejected, (state, action) => {
            state.status = 'failed';
            state.error = action.error.message || 'Something Error ...';
        });
        builder.addCase(fetchReduxOneData.fulfilled, (state, action) => {
            state.status = 'succeeded';
            state.data = action.payload;
        });
    },
});

export { fetchReduxOneData };
export const { updateData } = slice.actions;
export const selectReduxOneData = (state: RootState) => state.reduxOne;  // Related: store

export default slice.reducer;
```

## Companent

```tsx
import { FC, useEffect } from 'react';
import { useSelector, useDispatch } from 'react-redux';
import { fetchReduxOneData, updateData, selectReduxOneData } from '<redux slice file one>'

const Home: FC = () => {
    const dispatch = useDispatch();
    const reduxOneData = useSelector(selectReduxOneData);

    useEffect(() => {
        if (reduxOneData.status==='failed') {
            alert(reduxOneData.error);
        }
    }, [reduxOneData]);

    const handleClickUpdate = () => {
        dispatch(updateData('Hey Man Too ~'));
    }

    const handleClickUpdateAsync = () => {
        dispatch(fetchReduxOneData({ id: 1, age: 2 }));
    }

    return (
        <div>
            {reduxOneData.status === 'loading' ? 'loading ...' : reduxOneData.data}
            <button onClick={handleClickUpdate}>Update</button>
            <button onClick={handleClickUpdateAsync}>Async Update</button>
        </div>
    )
};

export default App;
```
